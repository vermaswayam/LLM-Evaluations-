# LLM Evaluation Methods: Programmatic, Human, and LLM-as-a-Judge

An LLM Evaluation Method defines **who or what executes the evaluation pipeline** to judge whether an LLM's output is good or bad. 

There are primarily three methods used to execute an evaluation: **Programmatic**, **Human**, and **Model-Graded (LLM-as-a-Judge)**.

---

## 1. Programmatic (Deterministic) Evaluation
This method uses hard-coded logic (e.g., Python scripts) to automatically calculate metrics. It is best used for components that have deterministic, clearly quantifiable success criteria.

*   **Example Use Case:** Evaluating the **Retriever** component of a RAG pipeline.
*   **Success Metric - `Recall@K`:** Out of all the truly relevant documents that exist in the database, how many did the system successfully retrieve in its top *K* results?
    *   *Scenario:* A user asks a question. The correct answer spans across 2 specific documents. The retriever is set to fetch 5 documents (`K=5`). If it fetches only 1 of the 2 correct documents, the `Recall@K` is 50%.
*   **Pros/Cons:** Fast, scalable, and cheap, but cannot evaluate subjective or nuanced textual answers.

## 2. Human Evaluation
This method involves human experts manually reviewing outputs against a rubric. It is used when the evaluation criteria are ambiguous or highly subjective.

*   **Example Use Case:** Evaluating a chatbot's overall "Helpfulness" on a scale of 1 to 5. (Helpfulness includes tone, completeness, and accuracy—things a Python script cannot easily measure).
*   **Pros/Cons:** Highly reliable and nuanced, but extremely expensive and impossible to scale for thousands of queries.
*   **Other ways Humans perform Evals:**
    1.  **Direct Grading/Rating:** Giving scores based on a rubric.
    2.  **Red Teaming:** Actively trying to break the system or bypass safety guardrails before launch.
    3.  **A/B Testing:** Users in production evaluating which model version they prefer.
    4.  **Golden Dataset Creation:** Experts mapping questions to their correct contexts to establish ground truth.
    5.  **Human-in-the-Loop:** Humans stepping in to evaluate edge cases where the automated system is uncertain.

## 3. Model-Graded (LLM-as-a-Judge)
The most popular middle-ground solution. It offers the scalability of programmatic evaluation with the nuanced understanding of a human evaluator. 

*   **Example Use Case:** Automating the grading of subjective exam answers (e.g., UPSC Mains). 
*   **The Workflow:**
    1.  You create a Golden Dataset where human experts grade a set of 50 subjective answers based on a strict rubric.
    2.  You pass the exact same 50 answers and the rubric to an LLM and ask it to act as a judge.
    3.  You compare the LLM's scores against the Human's scores.
*   **Success Metric - Mean Absolute Error (MAE):** You calculate the average deviation between the human scores and the LLM scores. If the MAE gets close to zero, the LLM is evaluating papers exactly like a human expert, and it is ready for production.

---

## Reference-Based vs. Reference-Free Evaluations

Every evaluation pipeline falls into one of these two categories based on how the dataset is structured.

### Reference-Based Evaluation
*   **Definition:** The Golden Dataset contains a predefined, known "Correct Answer" (a reference).
*   **How it works:** The evaluator compares the system's output directly against this ground truth.
*   *Examples:* Checking if a Retriever fetched the exact document ID specified in the dataset, or checking if the LLM-as-a-judge gave the exact same marks as the human expert.

### Reference-Free Evaluation
*   **Definition:** The dataset only contains inputs (questions/prompts). There is **no predefined correct answer**.
*   **How it works:** The evaluator (Human or LLM) judges the output strictly on its own merits against a conceptual rubric (e.g., "Is this safe?", "Is this polite?").
*   *Example:* Evaluating chatbot helpfulness. There is no single "perfect" string of text to compare against; the evaluator just decides if the generated text is helpful.

---

## Mind Map: LLM Evaluation Methods

```mermaid
mindmap
  root((LLM Eval Methods))
    Programmatic
      Executor: Code / Scripts
      Best For: Deterministic Tasks
      Example Metric: Recall@K
      Pros: Cheap & Scalable
      Cons: Cannot handle nuance
    Human
      Executor: Subject Matter Experts
      Best For: Subjective Tasks
      Types
        Direct Grading
        Red Teaming
        A/B Testing
        Golden Data Creation
        Human-in-the-loop
      Pros: Highly Reliable
      Cons: Expensive & Slow
    LLM-as-a-Judge
      Executor: Another LLM
      Best For: Scalable Nuance
      Example Metric: Mean Absolute Error MAE
      Goal: Mimic Human Grading
    Data Paradigms
      Reference-Based
        Has Ground Truth Answer
        Evaluates via Comparison
      Reference-Free
        No Predefined Answer
        Evaluates via Rubric
