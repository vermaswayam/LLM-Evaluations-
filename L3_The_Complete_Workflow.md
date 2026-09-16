# How to Evaluate LLM Applications: The Complete Workflow

When building an LLM application, you cannot simply build it and deploy it. You need a structured, repeatable workflow to ensure it performs as expected. This workflow remains the same whether you are building a simple classifier or a complex multi-agent system.

### A Practical Example: Customer Support Email Routing
Imagine building a system for Zomato that automatically reads incoming customer emails and routes them to the correct department (Billing, Technical, or General). Rather than deploying this immediately, you must run it through the evaluation workflow.

### The LLM Application Evaluation Workflow

1.  **Define Task and Target:** 
    Determine exactly what you are evaluating. 
    * *Example:* The entire email routing system (Target) is evaluated on its ability to classify emails correctly (Task).
2.  **Define Success Criteria (Metrics):** 
    Decide how you will measure success. 
    * *Example:* For a classification task, the metric is **Accuracy** (e.g., routing 90 out of 100 emails correctly = 90% accuracy).
3.  **Build a Golden Dataset:** 
    Create a ground-truth dataset to test the model against. Ideally, this contains 50–500 rows of real historical data manually labeled by a human. 
    * *Example:* `[Email Content: "App crashes on login" -> Expected Label: "Technical"]`.
4.  **Define Evaluation Method:** 
    Decide *who* or *what* will calculate the score.
    *   **Automated (Python Script):** Best for deterministic outputs (like checking if "Technical" == "Technical").
    *   **Human:** Best for subjective paragraphs, but expensive and unscalable.
    *   **LLM-as-a-Judge:** Using another LLM to compare generated textual paragraphs against expected answers.
5.  **Run the Model:** 
    Pass the Golden Dataset through your LLM system to generate predictions.
6.  **Evaluate & Analyze Results:** 
    Compare the model's predictions against the expected labels to get your score. If the score is low (e.g., 80%), analyze *why* it failed. (Are the prompts confusing? Is the model too small?).
7.  **Improve and Iterate (The Loop):** 
    Tweak the system (e.g., refine the prompt, upgrade the model) and run the *exact same Golden Dataset* through it again to see if the score improves. Repeat until you hit your target accuracy (e.g., 95%).
8.  **Deploy & Monitor:** 
    Once deployed, continuously monitor for production failures. 
    * *The Feedback Loop:* If the model misclassifies a real user email in production, grab that specific failure, add it to your Golden Dataset, and restart the evaluation loop to make the system more robust.

### Key Insight: Multiple Evals per Application
A single LLM application rarely has just one evaluation running. A production RAG system, for example, will have simultaneous, separate evals running for:
*   The Retriever component's accuracy.
*   The Embedding model's performance.
*   The overall End-to-End response quality.
*   Operational metrics like latency.

---

## Mind Map: The LLM Evaluation Workflow

```mermaid
flowchart TD
    A[1. Define Task & Target] --> B[2. Define Success Criteria / Metrics]
    B --> C[3. Build Golden Dataset]
    C --> D[4. Define Evaluation Method]
    
    subgraph Iteration Loop
        D --> E[5. Run Model on Dataset]
        E --> F[6. Evaluate Results]
        F --> G[7. Analyze Failures]
        G --> H[8. Improve System]
        H -.->|Change Prompt/Model| E
    end
    
    F -->|Target Reached| I[9. Deploy to Production]
    I --> J[10. Monitor for Failures]
    J -.->|Add failure edge cases| C
