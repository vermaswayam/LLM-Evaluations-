# Introduction to LLM Evaluations: Model vs. Application Evals

LLM Evaluations (Evals) are **systematic, repeatable tests** used to judge an LLM or an LLM-powered system against **clear criteria**. 

Unlike traditional software testing, LLM Evals cannot rely on "vibe testing" (casually asking the bot a few questions). They require structured datasets and pipelines to measure probabilistic outputs reliably.

### Core Characteristics of LLM Evals
1. **Systematic:** Uses curated datasets (e.g., pulling 100 real user queries) covering edge cases rather than random manual prompts.
2. **Repeatable:** The exact same dataset and testing pipeline can be re-run when you change the model, prompt, chunking strategy, or retriever to objectively compare Version 1 vs. Version 2.
3. **Clear Criteria:** Defines exactly what success looks like for a specific use case (e.g., correctness, tone, safety, grounding in context).

### Evals = The Entire Testing Setup
A common misconception is that an "Eval" is just a metric (like accuracy or precision). In the context of LLMs, the Eval is the **entire testing setup**. It encompasses:
* **What is being tested:** The whole RAG system, or just the retriever component?
* **The Criteria:** Are we testing for factual accuracy, latency, or formatting?
* **The Dataset:** The specific data used to run the test.
* **Environment:** Is it an offline test or production monitoring?
* **Tools:** Frameworks used for evaluation (e.g., Ragas).

---

## The Big Divide: Model Evals vs. Application Evals

To build production-grade AI, you must distinguish between evaluating the underlying model and evaluating the application built around it.

### 1. Model Evals (Evaluating the LLM itself)
* **Goal:** To test and benchmark the raw capabilities of a new foundation model. 
* **Target Audience:** Frontier labs (OpenAI, Anthropic, Google) building the models. As an AI Engineer, you just need to know how to read these to pick the right model for your architecture.
* **Capabilities Tested:** 
  * Reasoning 
  * World Knowledge 
  * Basic Math 
  * Coding
  * Instruction Following
  * Long Context Handling
  * Multimodal Understanding
  * Tool/API Usage
* **Common Benchmarks:**
  * **MMLU:** General knowledge and reasoning across subjects (law, medicine, etc.).
  * **GSM8K:** Grade school math word problems.
  * **SWE-bench:** Software engineering and coding capabilities.
  * **Needle In a Haystack:** Long context retrieval.

### 2. Application Evals (Evaluating the LLM System)
* **Goal:** To assess the behavior, performance, and safety of the *entire* LLM-powered application.
* **Target Audience:** AI/ML Engineers. **This is what you build and run.**
* **Why it's necessary:** The LLM is just the "processor" (brain). Your application includes the UI, system prompts, LangGraph orchestration, APIs, guardrails, chunking strategies, embedding models, and vector databases. A good processor doesn't guarantee a good phone if the battery or screen is terrible.
* **Scope:** 
  * **System-level:** Testing the final output (e.g., Did the chatbot answer correctly? Was the latency acceptable? Is it safe?).
  * **Component-level:** Testing individual parts (e.g., Did the retriever fetch the right chunks? Is the reranker ordering them correctly?).

---

## Mind Map: LLM Evaluation Ecosystem

```mermaid
mindmap
  root((LLM Evals))
    Core Characteristics
      Systematic Datasets
      Repeatable Pipelines
      Clear Criteria
    The Testing Setup
      Component vs System
      Metrics Defined
      Tooling e.g. Ragas
      Environment Offline vs Prod
    Model Evals
      Goal: Test Raw Capabilities
      Capabilities
        Reasoning
        Coding
        Tool Use
        Long Context
      Benchmarks
        MMLU General Knowledge
        GSM8K Math
        SWE-bench Code
        Needle in Haystack
    Application Evals
      Goal: Test Product Behavior
      Scope
        System Level
          End-to-End Latency
          Safety & Guardrails
          Cost per Token
        Component Level
          Retriever Hit Rate
          Embedding Quality
          Prompt effectiveness
      Stack
        Orchestration LangGraph
        Vector DBs
        APIs
