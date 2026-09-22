# Offline Evals vs. Online Evals

In the lifecycle of an LLM application, evaluation does not stop once the application is built. Evaluation happens continuously in two distinct phases: **Offline** (before deployment) and **Online** (after deployment in production). 

They are not rivals or replacements for one another; they are complementary systems. 
*   **Offline Evals** test if your application is working **correctly**.
*   **Online Evals** monitor if your application is running **normally**.

---

## 1. Offline Evals (Pre-Deployment)
Offline Evals are evaluations run on your software *before* it goes live to users. Everything discussed in previous chapters (Programmatic Evals, Golden Datasets, LLM-as-a-judge) falls under Offline Evals.

### Why do we need Offline Evals?
1.  **Pre-Release Testing (Gating):** In modern CI/CD pipelines, offline evals act as a "release gate." If a new update drops the success rate below a threshold (e.g., 95%), the deployment automatically halts.
2.  **Version Comparison:** If you want to decide between using Claude or OpenAI, or test two different vector databases, you run your Golden Dataset through both and compare the offline eval scores.
3.  **Regression Testing:** When you tweak a system prompt to make a chatbot more "polite," you run offline evals to ensure this new tweak doesn't break its ability to answer factual questions (regression).

---

## 2. Online Evals (Production/Post-Deployment)
Once deployed, real users will interact with your system. They will ask questions you never anticipated, mix languages, use slang, and attempt prompt injections. 

Offline evals cannot catch this because you don't have a Golden Dataset for future, unknown questions. This is where Online Evals step in.

### The Risks of Production
1.  **Unanticipated Inputs:** Edge cases, ambiguous prompts, and jailbreaks.
2.  **Emergent / Systematic Failures:** Issues that only appear at scale (e.g., latency spikes when 500 users log in, or subtle biases that only become apparent over 10,000 conversations).
3.  **Data Drift:** Over a year, your company's pricing, policies, and documents might change. Your offline Golden Dataset becomes obsolete, causing a disconnect between what the bot was tested on and what it is currently answering.

### The Online Eval Workflow
Because you don't have a pre-defined "Correct Answer" in production (Reference-Free Evaluation), the pipeline looks different:

1.  **Logging (The Foundation):** You must capture a structured, replayable record of every conversation turn. This includes metadata, context, output, latency, cost, and PII must be masked. Tools like **LangSmith** are heavily used here. Logging must be *non-blocking* (it shouldn't slow down the chat).
2.  **Capture vs. Compute Signals:**
    *   **Captured Signals:** Data you collect directly without effort (e.g., Latency, Token Cost, User Thumbs up/down).
    *   **Computed Signals:** Data you must actively calculate using an evaluator (e.g., Hallucination rate, Toxicity, Faithfulness). 
3.  **Sampling (For Computed Signals):** Running an "LLM-as-a-judge" on all 5,000 daily conversations to check for hallucinations is too expensive. Instead, you use **Stratified Sampling**—randomly picking 500 conversations, heavily prioritizing those with "Thumbs Down" or escalation keywords, and running the evaluator only on those.
4.  **Dashboarding & Alerting:** All signals (captured and computed) are aggregated over time (e.g., the last 1 hour) and displayed on a dashboard. If a metric crosses a baseline threshold (e.g., latency > 4 seconds, or hallucination rate > 15%), an automated alert is fired to a Slack channel via PagerDuty.

### The Feedback Loop (Closing the Loop)
When your Online Evals catch a failure in production (e.g., the bot hallucinated a refund policy), you extract that specific conversation log and add it to your **Offline Golden Dataset**. This ensures that before the next version of your app is deployed, it is explicitly tested against the edge case that previously broke it. 

---

## Mind Map: Offline vs. Online Evals

```mermaid
mindmap
  root((LLM Evals Lifecycle))
    Offline Evals
      Phase: Pre-Deployment
      Goal: Test for Correctness
      Data: Golden Dataset Reference-Based
      Use Cases
        CI/CD Gating
        Version Comparison
        Regression Testing
      Cost: Cheap & Fast
    Online Evals
      Phase: Post-Deployment Production
      Goal: Monitor for Normality
      Data: Live Traffic Reference-Free
      Production Risks
        Unanticipated Inputs
        Systematic Failures Load/Bias
        Data Drift
      The Pipeline
        1. Logging Mask PII
        2. Signal Processing
          Captured Latency, Thumbs Up
          Computed Hallucination Rate
        3. Stratified Sampling
        4. Dashboarding & Alerting
    The Feedback Loop
      Production Failure Detected
      Added to Offline Golden Dataset
      Model Improved & Re-tested
