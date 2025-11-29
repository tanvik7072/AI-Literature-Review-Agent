# AI Literature Review Agent: Specialised Multi-Agent Research Pipeline
__________________________________________________________________________

This project is a submission for the **5-Day AI Agents Intensive Course with Google and Kaggle Capstone Project**, demonstrating the development of a robust, multi-agent system for automating the academic literature review process.

## 1. The Problem

Academic research requires extensive and time-consuming literature reviews. The process is currently highly manual and inefficient; furthermore, the use of LLMs as an alternative often leads to LLMs hallucinating sources when asked to perform the synthesis task alone.


## 2. The Solution

The solution is a **Sequential Multi-Agent Pipeline** that decomposes the complex task of literature review into five steps. This design leverages the strengths of LLMs for high-level synthesis while enforcing reliability through custom tools. 

The the architecture is designed to address the aforementioned challenges in the following manner:

1. **Ensuring Source Integrity:** Ensuring that the generated review is based only on real papers found via Google Search.

2. **Maintaining Consistency:** Enforcing strict, recurring user preferences (e.g. "only 2024 papers") across independent research sessions.

3. **Agent Specialisation:** Separating the strictly functional tasks (filtering, citation) from the creative tasks (synthesis) to ensure accurate tool use.


### A. Multi-Agent System (Sequential)

The workflow is strictly managed by a `SequentialAgent` comprising five distinct, specialised sub_agents::

| Agent Name | Role | Output Key / Data Passed |
|:---|:---|:---|
| **OrchestratorAgent** | Processes the user query, retrieves memory, and sets the search plan. | `search_request` |
| **SearchAndRetrievalAgent** | Executes the Google Search to find raw paper data and abstracts. | `raw_papers_list` |
| **ProcessingAndFilteringAgent** | Scores and filters papers using the custom relevance tool. | `filtered_papers_list` |
| **BibliographyAgent** | Calls the `citation_formatter` to pre-format the references. | `bibliography_text` |
| **SynthesisAgent** | Writes the structured review. | `final_literature_review` |

### B. Custom Tools

The system integrates two custom tools to guarantee accurate output:

1. `citation_formatter` (Used by the `BibliographyAgent` to generate accurate bibliography text, overriding the LLM's tendency to hallucinate citations).

2. `calculate_relevance_score` (Used by the `ProcessingAndFilteringAgent` to automatically score each abstract based on the presence and frequency of keywords (defined based on the user's initial prompt). This consequently eliminates irrelevant papers.)

### C. Sessions & Memory (Long-Term Persistence)

* **Retrieval:** The `OrchestratorAgent` is equipped with the `preload_memory` tool, which searches the memory based on the current user query and embeds past preferences/conversations into the current agent's context.

* **Storage:** The `auto_save_orchestrator_turn_to_memory` callback is triggered after the initial run, ensuring the latest user constraints are saved to the memory bank for use in future, new sessions. The use of **Long-Term Memory** transforms the tool from a single-use one into a personalised research assistant that remembers and automatically applies user-defined constraints (such as publication dates, preferred topics etc.) across subsequent sessions.

## 3. Instructions for Setup (Kaggle Notebook)

This notebook is designed to run self-contained within the Kaggle environment.

### Prerequisites:

1. **Gemini API Key:** Ensure your Gemini API Key is saved in the Kaggle Secrets manager under the variable name `GOOGLE_API_KEY`.

2. **Internet Access:** Ensure "Internet" is enabled in the notebook settings.

### Execution Steps:

1. **Setup Cell:** Run the initial Python cell to load your `GOOGLE_API_KEY`.

2. **Code Cells:** Run all subsequent code cells sequentially to define the agents, tools, and the runner.

3. **Demonstration Cell:** Run the final code cells, which execute the two key sessions:

   * Session 1 (Basic Search)
   * Session 2 (Recalling)