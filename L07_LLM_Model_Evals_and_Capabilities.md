# LLM Model Evals & Capabilities

While previous sessions focused heavily on *Application Evals* (testing the entire software system built around an LLM), this session shifts focus to **Model Evals**. Model Evals are designed specifically to test the underlying capabilities of the Large Language Model (LLM) itself.

### Why do AI Engineers need Model Evals?
While Frontier Labs (like OpenAI or Anthropic) use Model Evals to train and improve their models, AI Engineers use them for practical decision-making:
1.  **Model Selection:** Justifying whether to use OpenAI's GPT, Anthropic's Claude, or an open-source model based on concrete metrics rather than guesswork.
2.  **Tracking Upgrades:** Evaluating if a newly released model (e.g., Claude 3.5 Sonnet vs. 3.0) actually improves performance for your specific use case.
3.  **Safety & Alignment:** Ensuring the base model is resistant to jailbreaks and hallucination before building an application on top of it.
4.  **Proprietary vs. Open Source:** Deciding whether to pay for a hosted API or deploy a cheaper open-source model locally based on capability trade-offs.

---

## Benchmarks vs. Custom Evals

When running a Model Eval, you have two primary ways to test the LLM:

### 1. Benchmarks (Standardized Tests)
Benchmarks (like MMLU or SWE-bench) are globally recognized, standardized tests. They are generic and test broad capabilities (math, coding, reasoning). 
*   *Pros:* Great for a quick, generic comparison between two frontier models.
*   *Cons:* A model that scores highest on a benchmark might be unnecessarily expensive or slow for your specific, simple use case.

### 2. Custom Evals (Task-Specific Tests)
Custom evals are tests you build using your own data. 
*   *Example:* You are building an email classifier for Zomato. Model A (Huge, Expensive) scores 99% on public Benchmarks. Model B (Small, Cheap) scores moderately on public Benchmarks. However, when you run your *Custom Eval* (testing both models on classifying 500 Zomato emails), Model A scores 94% and Model B scores 91%. 
*   *Conclusion:* For a minor 3% drop in accuracy, Model B saves significant latency and cost, making it the better engineering choice. You would *never* discover this by only looking at Benchmarks.

---

## The 8 Core Capabilities of LLMs

When Benchmarks are created, they usually target one of these 8 core capabilities:

1.  **Knowledge & Reasoning:** 
    *   Tests factual recall across subjects (e.g., Biology, Law) and the ability to connect multiple facts logically to reach a conclusion. This determines the model's overall "intelligence."
2.  **Coding & Software Engineering:** 
    *   Tests if the model can write functional code, debug, refactor large codebases, and interact with APIs. Highly critical for economic value (e.g., AI coding agents).
3.  **Mathematics:** 
    *   Tests symbolic and numerical reasoning ranging from grade-school math to competition-level Olympiad problems and research-level theorems.
4.  **Long Context Handling:** 
    *   Tests if the model can accurately retrieve specific facts ("Needle in a Haystack") or summarize information when given a massive prompt (e.g., 100k+ tokens) without degrading in quality.
5.  **Vision & Multimodal:** 
    *   Tests the model's ability to process, understand, and reason over images, graphs, and videos, not just text.
6.  **Agentic & Tool Use:** 
    *   Tests if the model can decide *when* and *how* to use external tools (like a web browser, calculator, or internal API) to complete a task.
7.  **Safety & Alignment:** 
    *   Tests if the model refuses harmful requests, resists prompt injections/jailbreaks, avoids generating toxic or biased content, and remains truthful (not a sycophant). Includes cybersecurity testing (e.g., spotting vulnerabilities).
8.  **Instruction Following:** 
    *   Tests if the model strictly obeys constraints (e.g., "Output exactly 200 words in JSON format without apologizing").

---

## Mind Map: Model Evals & Capabilities

```mermaid
mindmap
  root((Model Evals))
    Why AI Engineers Need Them
      Compare & Select Models
      Track Version Upgrades
      Evaluate Cost vs Performance
      Test Safety / Jailbreaks
    Evaluation Types
      Benchmarks
        Standardized
        Generic
        e.g. MMLU
      Custom Evals
        Task Specific
        Uses Internal Data
        Helps measure ROI
    The 8 Core Capabilities
      Knowledge & Reasoning
      Coding & SWE
      Mathematics
      Long Context Handling
      Vision & Multimodal
      Agentic & Tool Use
      Safety & Alignment
      Instruction Following
