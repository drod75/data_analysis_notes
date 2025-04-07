## Router Query Engine
Normally when making a rag you pass your query to the LLM where it uses the vector search tool to find the most common vector grabbed from your document(s). You can take this one step further by having a vector and summarize tool, a vector tool is just searching for the most similar vectors while a summarize tool is retrieving everything, so the whole document.

To start of we need to load in our document and get the nodes from it:

### This first block is used to get our open ai api key, this was just given in the lecture but is not to important, its just for ease of use
```python
from helper import get_openai_api_key

OPENAI_API_KEY = get_openai_api_key()
```

### This next block is to load in our documents, we simply specify the input files and then load our data, once we do this we can immediately go to the node and vector steps
```python
from llama_index.core import SimpleDirectoryReader

# load documents
documents = SimpleDirectoryReader(input_files=["metagpt.pdf"]).load_data()
```

### This next step allows us to split the document into chunks then get nodes from them.
```python
from llama_index.core.node_parser import SentenceSplitter

splitter = SentenceSplitter(chunk_size=1024)
nodes = splitter.get_nodes_from_documents(documents)
```

### SETTINGS!!! This next step allows us to get out model, we set our LLM and Embeddings model so we can use later
```python
from llama_index.core import Settings
from llama_index.llms.openai import OpenAI
from llama_index.embeddings.openai import OpenAIEmbedding

Settings.llm = OpenAI(model="gpt###3.5###turbo")
Settings.embed_model = OpenAIEmbedding(model="text###embedding###ada###002")
```


### We now can get our summary and vector index, like mentioned above summary will get all, and vector will get most similar
```python
from llama_index.core import SummaryIndex, VectorStoreIndex

summary_index = SummaryIndex(nodes)
vector_index = VectorStoreIndex(nodes)
```

### We now will start setting up our tools, by turning each index into a tool we can allow our LLM to start using our document and to become a true rag.
```python
summary_query_engine = summary_index.as_query_engine(
    response_mode="tree_summarize",
    use_async=True,
)
vector_query_engine = vector_index.as_query_engine()
```

### Now that we have made our engines we can turn on our tools, 
```python
from llama_index.core.tools import QueryEngineTool


summary_tool = QueryEngineTool.from_defaults(
    query_engine=summary_query_engine,
    description=(
        "Useful for summarization questions related to MetaGPT"
    ),
)

vector_tool = QueryEngineTool.from_defaults(
    query_engine=vector_query_engine,
    description=(
        "Useful for retrieving specific context from the MetaGPT paper."
    ),
)
```

### To retrieve our data we can either use the LLM to get json files or an api to get pydantic objects, in this case we will use our LLM.
```python
from llama_index.core.query_engine.router_query_engine import RouterQueryEngine
from llama_index.core.selectors import LLMSingleSelector


query_engine = RouterQueryEngine(
    selector=LLMSingleSelector.from_defaults(),
    query_engine_tools=[
        summary_tool,
        vector_tool,
    ],
    verbose=True
)

response = query_engine.query("What is the summary of the document?")
print(str(response))

```

### If lets say we wanted to make a new rag but with a different document, we can do this:
```python
from utils import get_router_query_engine

query_engine = get_router_query_engine("metagpt.pdf")

response = query_engine.query("Tell me about the ablation study results?")
print(str(response))

```

## Tool Calling
In this part we can tell and provide our LLM with functions to use as tools, an example to start with is:

```python
from llama_index.core.tools import FunctionTool

def add(x: int, y: int) ###> int:
    """Adds two integers together."""
    return x + y

def mystery(x: int, y: int) ###> int: 
    """Mystery function that operates on top of two numbers."""
    return (x + y) ### (x + y)


add_tool = FunctionTool.from_defaults(fn=add)
mystery_tool = FunctionTool.from_defaults(fn=mystery)
```

```python
from llama_index.llms.openai import OpenAI

llm = OpenAI(model="gpt###3.5###turbo")
response = llm.predict_and_call(
    [add_tool, mystery_tool], 
    "Tell me the output of the mystery function on 2 and 9", 
    verbose=True
)
print(str(response))
```

In our code we are using our two functions to make some tools, then we are giving them to our LLM so our query has tools it can call on to meet our request. 

### This example is where instead of using an LLM to retrieve data we use meta data to do so instead, in our example we make our vector index as normal and when we make our query engine we specify some meta data filters so we can make our query.
```python
from llama_index.core import VectorStoreIndex
from llama_index.core.vector_stores import MetadataFilters

vector_index = VectorStoreIndex(nodes)
query_engine = vector_index.as_query_engine(similarity_top_k=2)

query_engine = vector_index.as_query_engine(
    similarity_top_k=2,
    filters=MetadataFilters.from_dicts(
        [
            {"key": "page_label", "value": "2"}
        ]
    )
)

response = query_engine.query(
    "What are some high###level results of MetaGPT?", 
)
```

### We will now combine our function tool and metadata rag to make it so that based on our query and metadata filters our LLM will think of relevant filters for our query, this results in more precise retrieval since our LLM will be restricted to more concentrated content.
```python
from typing import List
from llama_index.core.vector_stores import FilterCondition


def vector_query(
    query: str, 
    page_numbers: List[str]
) ###> str:
    """Perform a vector search over an index.
    
    query (str): the string query to be embedded.
    page_numbers (List[str]): Filter by set of pages. Leave BLANK if we want to perform a vector search
        over all pages. Otherwise, filter by the set of specified pages.
    
    """

    metadata_dicts = [
        {"key": "page_label", "value": p} for p in page_numbers
    ]
    
    query_engine = vector_index.as_query_engine(
        similarity_top_k=2,
        filters=MetadataFilters.from_dicts(
            metadata_dicts,
            condition=FilterCondition.OR
        )
    )
    response = query_engine.query(query)
    return response
    

vector_query_tool = FunctionTool.from_defaults(
    name="vector_tool",
    fn=vector_query
)
```

### Here we use the same LLM function as before but the tool is a metadata query engine so our results will be more concise and accurate.
```python
llm = OpenAI(model="gpt###3.5###turbo", temperature=0)
response = llm.predict_and_call(
    [vector_query_tool], 
    "What are the high###level results of MetaGPT as described on page 2?", 
    verbose=True
)
```

### Now we will combine our previous lesson to create a tool picking system!
```python
from llama_index.core import SummaryIndex
from llama_index.core.tools import QueryEngineTool

# setting up summary tool
summary_index = SummaryIndex(nodes)
summary_query_engine = summary_index.as_query_engine(
    response_mode="tree_summarize",
    use_async=True,
)
summary_tool = QueryEngineTool.from_defaults(
    name="summary_tool",
    query_engine=summary_query_engine,
    description=(
        "Useful if you want to get a summary of MetaGPT"
    ),
)

# will use vector tool
response = llm.predict_and_call(
    [vector_query_tool, summary_tool], 
    "What are the MetaGPT comparisons with ChatDev described on page 8?", 
    verbose=True
)

# will use summary tool
response = llm.predict_and_call(
    [vector_query_tool, summary_tool], 
    "What is a summary of the paper?", 
    verbose=True
)

```

## Building an Agent Reasoning Loop
Despite our useful code its limiting since our LLM  gets our query, runs a tool and goes back, if we ask a complex question with multiple steps it won’t really work and will have trouble getting a good answer. This is where and Agent Reasoning Loop comes in, an agent will be able to reason with its tools multiple times.

Our agent is made up of the agent runner and worker, our runner assigns a task id the task, the completed steps, and the steps in the queue, it passes this to our worker which has our tools and LLM, it will then pass this to the runner again until everything is done.

```python
from utils import get_doc_tools

vector_tool, summary_tool = get_doc_tools("metagpt.pdf", "metagpt")

from llama_index.llms.openai import OpenAI

llm = OpenAI(model="gpt###3.5###turbo", temperature=0)

from llama_index.core.agent import FunctionCallingAgentWorker
from llama_index.core.agent import AgentRunner

agent_worker = FunctionCallingAgentWorker.from_tools(
    [vector_tool, summary_tool], 
    llm=llm, 
    verbose=True
)
agent = AgentRunner(agent_worker)

response = agent.query(
    "Tell me about the agent roles in MetaGPT, "
    "and then how they communicate with each other."
)
```

In the response it has steps, it calls the function, gets the output, repeats these two, then our LLM takes this in to give our answer, this is chain of thought.

With agent control we can have: 
### More flexibility with tasks, 
### Deeper insights for each step allowing for debugging
### The ability to steer the LLM into the right direction.

### Debugging code example with steering
```python
agent_worker = FunctionCallingAgentWorker.from_tools(
    [vector_tool, summary_tool], 
    llm=llm, 
    verbose=True
)
agent = AgentRunner(agent_worker)

#create task
task = agent.create_task(
    "Tell me about the agent roles in MetaGPT, "
    "and then how they communicate with each other."
)

# get current step output
step_output = agent.run_step(task.task_id)

# look at the completed steps
completed_steps = agent.get_completed_steps(task.task_id)
print(f"Num completed for task {task.task_id}: {len(completed_steps)}")
print(completed_steps[0].output.sources[0].raw_output)

# look at next steps, with this we can stop at any point
upcoming_steps = agent.get_upcoming_steps(task.task_id)
print(f"Num upcoming steps for task {task.task_id}: {len(upcoming_steps)}")
upcoming_steps[0]

# step output with input to steer
step_output = agent.run_step(
    task.task_id, input="What about how agents share information?"
)

# last step output
step_output = agent.run_step(task.task_id)
print(step_output.is_last)

# create an agent response by finalizing our work
response = agent.finalize_response(task.task_id)
print(str(response))
```

##  Building a Multi-Document Agent
Now we will make an Agent that has multiple documents it can search through, our setup will be:

### Docs
```python
urls = [
    "https://openreview.net/pdf?id=VtmBAGCN7o",
    "https://openreview.net/pdf?id=6PmJoRfdaK",
    "https://openreview.net/pdf?id=hSyW5go0v8",
]

papers = [
    "metagpt.pdf",
    "longlora.pdf",
    "selfrag.pdf",
]
```

### tools for each doc
```python
from utils import get_doc_tools
from pathlib import Path

paper_to_tools_dict = {}
for paper in papers:
    print(f"Getting tools for paper: {paper}")
    vector_tool, summary_tool = get_doc_tools(paper, Path(paper).stem)
    paper_to_tools_dict[paper] = [vector_tool, summary_tool]

initial_tools = [t for paper in papers for t in paper_to_tools_dict[paper]]
```

### LLM
```python
from llama_index.llms.openai import OpenAI

llm = OpenAI(model="gpt###3.5###turbo")
```

All we did was get our tools for each doc and LLM, our agent has a doc, and for each doc a vector and summary tool. When we ask our agent a question it will use the right tools based on the document it needs.

### agent with multi docs
```python
from llama_index.core.agent import FunctionCallingAgentWorker
from llama_index.core.agent import AgentRunner

agent_worker = FunctionCallingAgentWorker.from_tools(
    initial_tools, 
    llm=llm, 
    verbose=True
)
agent = AgentRunner(agent_worker)

response = agent.query(
    "Tell me about the evaluation dataset used in LongLoRA, "
    "and then tell me about the evaluation results"
)

response = agent.query("Give me a summary of both Self###RAG and LongLoRA")
print(str(response))
```

Now we will try with 11 papers and like last time, this can take time:

### papers
```python
from utils import get_doc_tools
from pathlib import Path

paper_to_tools_dict = {}
for paper in papers:
    print(f"Getting tools for paper: {paper}")
    vector_tool, summary_tool = get_doc_tools(paper, Path(paper).stem)
    paper_to_tools_dict[paper] = [vector_tool, summary_tool]

all_tools = [t for paper in papers for t in paper_to_tools_dict[paper]]
```

With all these tools it may be hard for our LLM to know what to do, so we will do rag on our tools to retrieve the most relevant tool for our query.

### rag tools
```python
# define an "object" index and retriever over these tools
from llama_index.core import VectorStoreIndex
from llama_index.core.objects import ObjectIndex

obj_index = ObjectIndex.from_objects(
    all_tools,
    index_cls=VectorStoreIndex,
)

obj_retriever = obj_index.as_retriever(similarity_top_k=3)


```

### agent with rag tools
```python
from llama_index.core.agent import FunctionCallingAgentWorker
from llama_index.core.agent import AgentRunner

agent_worker = FunctionCallingAgentWorker.from_tools(
    tool_retriever=obj_retriever,
    llm=llm, 
    system_prompt=""" \
You are an agent designed to answer queries over a set of given papers.
Please always use the tools provided to answer a question. Do not rely on prior knowledge.\

""",
    verbose=True
)
agent = AgentRunner(agent_worker)

response = agent.query(
    "Tell me about the evaluation dataset used "
    "in MetaGPT and compare it against SWE###Bench"
)
print(str(response))
```

Now with this, we can have concise results based on carefully chosen tools from all of our documents, our LLM uses tools chosen via RAG to then initiate a chain of thought to complete multiple steps of work witht he right tools for the job to get the best results, we have built a good and very precise RAG Agent.