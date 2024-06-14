# AI Agents in LangGraph

Calvin Wetzel

## Build an Agent from Scratch

- Agent class
- Prompt Template
  - Provide Prompts and Observations
    - Model returns Thoughts/answers

## LangGraph Components

- Prompt Templates allow reusable prompts with formatted variables; formatted variables can come from user content
- LangGraph helps orchestrate control flow and cyclic graphs
  - ensures persistence (memory of conversation)
- Graphs
  - Nodes -> agents or functions
  - Edges -> connect nodes
  - Conditional Edges -> decisions
- Agent State is accessible to all parts of the graph
  - can be stored in persistence layer
  
![Search Tool Schematic](img/SearchToolSkematic.png)
