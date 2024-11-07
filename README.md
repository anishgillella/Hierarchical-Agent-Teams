# Hierarchical Agent Teams

This project demonstrates a multi-agent workflow for collaborative research and document authoring using LangChain, LangGraph, and OpenAI's GPT models. The workflow integrates two primary agent teams—**Research Team** (for gathering information) and **Document Writing Team** (for writing, organizing, and editing documents)—with a supervisory layer that orchestrates task flow within and between teams.

## Project Structure

The workflow is organized into three main layers:
1. **Research Team**: Handles information gathering and web scraping tasks.
2. **Document Writing Team**: Manages document creation, note-taking, chart generation, and editing.
3. **Supervisory Layer**: Oversees task flow between the Research and Document Writing teams, directing actions based on the overall workflow context.

## Key Components

### Research Team
- **Agents**:
  - **Search Agent**: Uses the Tavily tool to perform structured data searches.
  - **WebScraper Agent**: Utilizes web scraping tools to extract information from websites.
- **Supervisor**: Manages task flow between `Search` and `WebScraper` agents using a `StateGraph`, which allows seamless task transitions.

### Document Writing Team
- **Agents**:
  - **DocWriter**: Handles document creation and editing.
  - **NoteTaker**: Manages outline creation and note organization.
  - **ChartGenerator**: Generates charts based on document content.
- **Supervisor**: Routes tasks among `DocWriter`, `NoteTaker`, and `ChartGenerator`, coordinating document production steps.

### Supervisory Layer
The top-level graph (`super_graph`) orchestrates interactions between the **Research Team** and **Document Writing Team**, initiating tasks, managing transitions, and completing the workflow once all tasks are finished.

## Setup and Installation

### Prerequisites
- Python 3.8 or higher
- Jupyter Notebook or JupyterLab (optional, for interactive visualization)

### Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/anishgillella/Hierarchical-Agent-Teams.git
   cd Hierarchical-Agent-Teams
   ```

2. Install dependencies:
   ```python
   %pip install -U langchain langgraph langchain_openai langchain_experimental
   ```

3. Set up API keys for OpenAI and Tavily:
   ```python
   import getpass
   import os

   def set_if_undefined(var: str):
       if var not in os.environ:
           os.environ[var] = getpass.getpass(f"Please enter the {var} key: ")

   set_if_undefined('OPENAI_API_KEY')
   set_if_undefined('TAVILY_API_KEY')
   os.environ["LANGCHAIN_TRACING_V2"] = "true"
   ```

## Project Overview

### 1. Research Team Workflow
The **Research Team** includes:
- **Search Agent**: Utilizes the Tavily tool to perform structured data searches.
- **WebScraper Agent**: Employs web scraping tools for detailed information extraction.

The Research Team supervisor directs tasks between the `Search` and `WebScraper` agents using a `StateGraph`.

### 2. Document Writing Team Workflow
The **Document Writing Team** includes:
- **DocWriter**: Creates and edits documents.
- **NoteTaker**: Writes outlines and organizes notes.
- **ChartGenerator**: Generates charts based on document content.

The Document Writing Team supervisor manages routing between these agents, ensuring smooth document production.

### 3. Supervisory Layer Workflow
The top-level graph (`super_graph`) manages interactions between the **Research Team** and **Document Writing Team**. This layer decides which team should act based on task requirements and coordinates the overall workflow.

## Usage

Execute the following commands in sequence to run the workflows:

- **Research Team Workflow**:
  ```python
  research_chain.stream("When is Taylor Swift's next tour?", {"recursion_limit": 100})
  ```

- **Document Writing Team Workflow**:
  ```python
  authoring_chain.stream("Write an outline for a poem and then write the poem to disk.", {"recursion_limit": 100})
  ```

- **Top-Level Workflow**:
  ```python
  for s in super_graph.stream(
      {
          "messages": [
              HumanMessage(
                  content="Write a brief research report on the North American sturgeon. Include a chart."
              )
          ]
      },
      {"recursion_limit": 150},
  ):
      if "__end__" not in s:
          print(s)
  ```

## Visualization

The `StateGraph` enables visualization of the workflow. Use `draw_mermaid_png()` to generate a visual representation:

```python
from IPython.display import display, Image

display(Image(chain.get_graph(xray=True).draw_mermaid_png()))
display(Image(super_graph.get_graph(xray=True).draw_mermaid_png()))
```

## Potential Applications

- Automated research and reporting systems
- Collaborative document authoring
- Information retrieval and summarization pipelines

## License

This project is licensed under the MIT License. See the `LICENSE` file for more information.

## Contributing

Contributions are welcome! Please fork this repository, create a new branch, and submit a pull request.
