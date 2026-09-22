# Selecting the Right LLM: Running Custom Model Evals

While public LLM Leaderboards are excellent for *filtering* down hundreds of models to a top 5-10 list, they are not meant for making the final selection. To choose the absolute best model for your specific AI application, you must run a **Custom Model Eval** on your own proprietary data. 

This session demonstrates a full end-to-end practical case study: Building a Text-to-SQL system for ESPNcricinfo and selecting the best LLM to power it.

---

## The Case Study: ESPNcricinfo Text-to-SQL
**The Problem:** During a live India vs. Pakistan match, ESPNcricinfo receives thousands of queries per minute from fans asking historical cricket questions (e.g., "What is Virat Kohli's average against Pakistan?"). Analysts manually writing SQL queries to answer these cannot scale.
**The Solution:** Build a "Text-to-SQL" feature where users type questions in plain English, and an LLM converts it into an SQL query, runs it against the database, and returns the result.

### Step 1: Gather Requirements (The Constraints)
Before writing any code, an AI Engineer must define the constraints with product managers:
1.  **Task:** Text-to-SQL generation based on a provided database schema.
2.  **Cost Ceiling:** ₹3 Lakhs per month max budget.
3.  **Latency:** Must return a query within 2-3 seconds (live user experience).
4.  **Context Window:** Small. (No multi-turn memory needed, just the schema and the prompt).
5.  **Deployment:** Public APIs (OpenAI, Anthropic, etc.) are fine; no strict on-premise privacy requirement.
6.  **Correctness:** Extremely high priority. Cricket fans are notoriously picky; hallucinating a stat will cause a PR disaster.

**Cost Calculation & Prompt Caching:**
*   Input Tokens per query (Schema + Instructions): ~400 tokens.
*   Output Tokens per query (SQL statement): ~100 tokens.
*   *Optimization:* Because the 400-token input (the Schema) remains identical across 50,000 daily queries, you can use **Prompt Caching**. You pay a slight premium to cache the prompt for 5 minutes, but subsequent queries within that window cost 90% less on input tokens, turning a ₹12 Lakh/month bill into a ₹2-3 Lakh/month bill.

### Step 2: Shortlist Candidate Models
Using a Composite Leaderboard (like *llmstats.com*), the 146 available models were filtered:
1.  Eliminated any model that exceeded the ₹5 Lakh/month budget based on the 4:1 Input/Output ratio.
2.  Ranked the remaining models using a normalized weighted formula: `(90% * Coding Capability Score) + (10% * Speed/Latency Score)`. Speed was weighted lower because printing a 100-character SQL query is fast even on slower models.
3.  **The Top 5 Candidates:** GPT-4.5 Terra, Kimi K3 (China's new frontier model), Grok 4.5, Claude 3.5 Sonnet, and Minimax M3 (Open Source).

### Step 3: Run the Custom Eval
This is the core of AI Engineering—testing the top 5 models against your specific data.

1.  **The Setup:** Load a subset of IPL data (2020-2024) into an SQLite database. Extract the database schema (Table names, Column names, Data types) into a `.sql` file to feed to the LLMs.
2.  **Build the Golden Dataset:** A Data Analyst manually writes 20-50 highly diverse cricket questions (easy, medium, hard, joins, sub-queries) and manually writes the *correct* SQL query for each.
3.  **The Evaluator Logic:** How do we automatically grade if the LLM generated the correct SQL?
    *   *You cannot use simple string matching.* Two different SQL queries can yield the exact same correct result.
    *   *The Logic:* The Python script takes the `LLM_Generated_SQL` and the `Golden_SQL`. It runs *both* against the SQLite database. It extracts the resulting tables, normalizes the data types (e.g., matching `2.0` with `2`), sorts the rows, and compares the final tables. If Table A matches Table B, the LLM passes.
4.  **The Execution:** Using *OpenRouter* to hit all 5 model APIs from one script, the eval loops through all 20 questions for each model.

### The Results & Conclusion
*   **Kimi K3 (2.7T Parameters):** Fails miserably. Around 55% accuracy, extremely slow, and threw multiple SQL syntax errors. *Lesson: Never blindly trust leaderboard hype.*
*   **Minimax M3:** Failed early with SQL syntax errors.
*   **GPT-4.5 Terra:** 80% accuracy. Very good, but very expensive.
*   **Claude 3.5 Sonnet:** 85% accuracy. Extremely fast latency.
*   **Grok 4.5:** 90% accuracy. Highest performance, lowest cost among the top 3.

**Final Decision:** The choice comes down to Grok 4.5 vs. Claude 3.5 Sonnet. While Grok scored slightly higher, the team might select Claude Sonnet for its enterprise API reliability and lower latency, trading a 5% accuracy drop for higher production stability.

---

## Mind Map: The Custom Eval Workflow

```mermaid
flowchart TD
    A[1. Define Constraints] --> B[Task: Text-to-SQL]
    A --> C[Budget: 3L / month]
    A --> D[Latency: 2-3 sec]

    B --> E[2. Leaderboard Filtering]
    C --> E
    D --> E

    E --> F[Filter by Cost]
    F --> G[Rank by Formula: 90% Coding + 10% Speed]
    G --> H[Shortlist Top 5 Models]

    H --> I[3. Custom Eval Execution]

    subgraph The Custom Eval Loop
        I --> J[Load SQLite DB & Schema]
        J --> K[Create Golden Dataset Questions + Correct SQL]
        K --> L[Pass Prompt + Schema to LLM]
        L --> M[LLM Generates SQL]
        
        M --> N[Run LLM SQL on DB]
        K --> O[Run Golden SQL on DB]
        
        N --> P[Compare Output Tables]
        O --> P
        P --> Q{Match?}
    end
    
    Q -->|Yes| R[Pass]
    Q -->|No| S[Fail]
    
    R --> T[Calculate Final Accuracy]
    S --> T
    
    T --> U[Select Best Model for Prod]
