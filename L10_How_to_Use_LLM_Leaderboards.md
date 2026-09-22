# How to Use LLM Leaderboards

An LLM Leaderboard is a public ranking or comparison table that shows how different LLMs perform on a common set of evaluations or benchmarks. If benchmarks are the "exam," the leaderboard is the "report card" published on the notice board.

### Why do Leaderboards Exist?
1.  **Standardized Comparison:** They provide a common reference point to compare models across different labs (OpenAI, Anthropic, Google).
2.  **Trust via Third Parties:** A leaderboard run by an independent third party is far more trustworthy than a frontier lab publishing its own internal marketing numbers.
3.  **Saves Resources:** Running benchmarks across 100+ models yourself requires immense time and compute costs. Leaderboards do this heavy lifting for you.
4.  **Detecting Saturation:** When all top models start clustering around the exact same score (e.g., 92-94%), the leaderboard clearly shows that the underlying benchmark is saturated.
5.  **Model Discovery:** They help AI Engineers discover new, smaller, or cheaper open-source models that they might not have heard of otherwise.

---

## Types of LLM Leaderboards

There are four primary categories of leaderboards:

### 1. Benchmark-Specific Leaderboards
*   **What it is:** Ranks models based on a *single* benchmark (e.g., only GSM8K or only HLE). 
*   **Pros/Cons:** Useful for deep dives into one capability, but gives a very narrow view of the model's overall utility.

### 2. Multi-Benchmark (Composite) Leaderboards
*   **What it is:** Combines scores from multiple benchmarks across different capabilities (Knowledge, Coding, Math) into one cumulative ranking. Platforms like *Artificial Analysis* or *LiveBench* fall here.
*   **Pros/Cons:** Extremely useful because they also provide operational metadata like Cost-per-token, Latency, and Context Window size.

### 3. Human Preference (Crowdsourced) Leaderboards
*   **What it is:** Instead of automated scripts, these use "blind A/B testing" by real humans. A user asks a question, two anonymous models answer, and the user votes on which answer is better. *LMSYS Chatbot Arena* is the most famous example.
*   **Pros/Cons:** Captures "vibes" and helpfulness better than code, but suffers from human bias (humans tend to vote for longer, better-formatted, or more confident-sounding answers, even if factually weaker).

### 4. Application-Specific Leaderboards
*   **What it is:** Ranks models exclusively on their ability to perform a specific engineering task.
*   **Example:** *Berkeley Function Calling Leaderboard* ranks models purely on their ability to use tools and call APIs. *MTEB* ranks embedding models specifically for RAG architectures.

---

## Why You Shouldn't Blindly Trust Leaderboards

As an AI Engineer, you must view leaderboards with skepticism due to:
*   **Real-World Disconnect:** A model that scores 90% on clean benchmark data might fail on your company's messy, ambiguous, domain-specific data.
*   **Contamination:** Models may have memorized the benchmark dataset during training, inflating their score.
*   **Goodhart's Law (Over-optimization):** "When a measure becomes a target, it ceases to be a good measure." Companies now actively fine-tune their models specifically to win on LMSYS Chatbot Arena (e.g., training them to write longer, friendlier answers) rather than improving actual capabilities.
*   **Aggregation Hiding:** Composite leaderboards might use opaque weighting systems. You don't know how much "Coding" vs "Math" contributed to the final score.
*   **Stale Data:** Leaderboards are often outdated and might not feature the newest model iterations.

---

## The Engineer's Guide to Using Leaderboards

**Master Rule: Leaderboards are a FILTERING tool, not a DECISION tool.**

Follow this workflow when selecting a model for a project:
1.  **Define Constraints First:** Before looking at any leaderboard, write down your hard constraints: Maximum latency, budget per million tokens, deployment type (API vs. On-Premise/Open Source).
2.  **Pick the Right Leaderboard:** If building an agent, go to the Berkeley Function Calling Leaderboard. If building a RAG pipeline, check MTEB.
3.  **Read the Fine Print:** Look at how the leaderboard aggregates scores, the inference budget used, and whether the data is fresh.
4.  **Shortlist 3 to 5 Models:** Use the leaderboard to narrow down the hundreds of available models to a handful that fit your constraints.
5.  **Run Custom Evals:** Take the top 5 models and run them against your own private, custom dataset (Custom Evals) using your exact system prompt. **The winner of your custom eval is the model you deploy.**

---

## Mind Map: LLM Leaderboards

```mermaid
mindmap
  root((LLM Leaderboards))
    Why use them?
      Standardized Comparison
      Model Discovery
      Resource Saving
      Detects Saturation
    Types of Leaderboards
      Benchmark-Specific Narrow focus
      Composite e.g. LiveBench, Artificial Analysis
      Human Preference e.g. LMSYS Chatbot Arena
      Application-Specific e.g. Berkeley Function Calling
    Pitfalls
      Real-World Disconnect
      Contamination
      Goodhart's Law Over-optimization
      Opaque Aggregation Weights
      Stale Data
    Engineering Workflow
      1. Define Hard Constraints Cost, Latency
      2. Consult Relevant Leaderboard
      3. Shortlist Top 3-5 Models
      4. Run Custom Evals
      Result: Leaderboards = Filter, Custom Evals = Decision
