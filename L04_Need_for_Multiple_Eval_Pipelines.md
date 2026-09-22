# Why Your AI Application Needs Multiple Eval Pipelines

Building a single evaluation pipeline is rarely enough for a production-grade LLM application. As an AI Engineer, you will almost always need to implement **multiple, concurrent evaluation pipelines** for a single application. 

This is driven by two primary factors: **Multiple Failure Points** and **Multiple Risk Categories**.

### 1. Multiple Failure Points
An LLM application (like a RAG chatbot or an AI agent) is built with multiple independent components. Each of these components, and the way they interact, can fail independently. 

Evaluations must happen at three distinct levels:
*   **Component Level:** Evaluating individual pieces in isolation. 
    * *Example in RAG:* The Retriever might fetch the wrong documents. The Generator (LLM) might hallucinate. You need one eval pipeline just for the Retriever, and another just for the Generator.
*   **Workflow / Pipeline Level:** Evaluating the interaction between components. 
    * *Example:* The Retriever fetches the correct document, but places it at the very bottom of the context window. The Generator prioritizes the top documents and gives a wrong answer. Both components worked correctly in isolation, but the workflow failed. You need a workflow-level eval (which might suggest adding a Re-ranker) to catch this.
*   **Application Level:** Evaluating the final user experience.
    * *Example:* The system provides a perfectly accurate, grounded answer, but takes 10 seconds to generate it. The application level fails due to latency.

### 2. Multiple Risk Categories
Even if you are evaluating a specific component or workflow, you must evaluate it across multiple dimensions (Risk Categories). A single eval pipeline usually only tests one dimension. 

Risk categories are broadly divided into three buckets:

**A. Application Quality (Does it do its job well?)**
*   **General LLM Apps:** Correctness, Relevance, Completeness, Instruction Following.
*   **RAG Specific:** 
    * *Context Relevance / Retriever Recall:* Did it fetch the right documents?
    * *Groundedness / Faithfulness:* Is the generated answer based *only* on the retrieved context?
    * *Citation Accuracy:* Can it accurately cite the source?
*   **Agent Specific:** Tool Selection accuracy, Parameter correctness, Error Recovery (can it bounce back if a tool fails?), Task Completion rate.
*   **Multi-turn Chatbots:** Context Retention (memory), Clarification Behavior (does it ask clarifying questions when confused?).

**B. Safety (Is the output harmless?)**
*   **Toxicity & Harmful Content:** Does it output hate speech, self-harm instructions, or illegal advice?
*   **Bias:** Does it treat all user profiles fairly?
*   **PII Leakage:** Does it accidentally reveal personal credit card info or emails?
*   **Jailbreak Resistance:** Can a user manipulate the prompt to bypass guardrails?

**C. Operations (Is it fast, cheap, and reliable?)**
*   Latency (Time to First Token)
*   Cost per request / Token Efficiency
*   Error & Failure rates under load

### Summary
Because an AI application has multiple points of failure (Components -> Workflows -> Application) and multiple risks to mitigate (Quality + Safety + Operations), a production system requires a matrix of evaluation pipelines working together to ensure reliability.

---

## Mind Map: The Multi-Eval Ecosystem

```mermaid
mindmap
  root((Multiple Eval Pipelines))
    Why Multiple?
      Multiple Failure Points
      Multiple Risk Categories
    Failure Points (Levels)
      Component Level
        Retriever
        Generator
        Embedding Model
      Workflow Level
        Component Interactions
        Context Ordering
      Application Level
        End-to-End Latency
        Final User Experience
    Risk Categories
      App Quality
        RAG: Groundedness & Context Relevance
        Agents: Tool Selection & Error Recovery
        General: Correctness & Instruction Following
      Safety
        Jailbreak Resistance
        PII Leakage
        Toxicity & Bias
      Operations
        Cost per Token
        Latency
        Load Reliability
