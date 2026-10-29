# Process Design Write-up

### 1. AI vs. rules
**AI** is used exclusively for unstructured text comprehension: extracting specific facts (e.g., warranty length, timeline) from dense PDFs and providing qualitative 0-10 scores based on the admin's rubric. 
**Rules (Plain Code)** are used for binary logic (Pass/Fail criteria), math (multiplying the 0-10 scores by the admin's percentage weights), and security checks. 
**Why?** Large Language Models excel at semantic understanding but are inherently unreliable at arithmetic and strict logical gating. Drawing the line here prevents the AI from hallucinating math or accidentally passing a mandatory requirement due to a probabilistic error.

### 2. Consistency and explainability
To guarantee repeatability, the AI temperature is set to `0.0`, and the architecture is split into two steps: Fact Extraction and Scoring. 
For explainability, the AI is required to output an `evidence_quote` for every data point it extracts. When a reviewer clicks on a proposal's rank, the UI doesn't just show an arbitrary number; it highlights the exact sentence from the proposal that justified the score, alongside the AI's reasoning. If ranked twice, the deterministic rule-based math and temperature-0 extraction ensure the same input yields the same result.

### 3. Human in the loop
The AI does not make the final decision; it acts as a smart sorting algorithm. The reviewer dashboard displays the AI's ranked list, but the human must click "Approve Award" to finalize it. Reviewers can manually override any AI-generated score or reinstate a "Flagged" or "Ineligible" proposal. If a human changes a score, they must select a reason from a dropdown, which is permanently logged in a tamper-proof audit table for compliance.

### 4. Changing criteria
Because extraction is decoupled from scoring, changing the math is trivial. If an admin edits the percentage weights, the system instantly recalculates the final scores using plain code, without re-running the AI. If an admin edits the text of the criteria themselves, the system simply re-runs Step 7 (AI Qualitative Scoring) using the previously extracted JSON facts, saving time and API costs.

### 5. Risks and safeguards
*   **Prompt Injection / Manipulation:** (e.g., Proposal C's attempt to force "full marks"). Guarded by a two-stage process. First, the prompt instructs the AI to *only* extract facts, not make decisions. Second, a security filter flags keywords like "ignore previous instructions" or "automated review."
*   **Made-up facts (Hallucinations):** Guarded by requiring the LLM to provide exact substrings (`evidence_quote`) from the source text. If the quote doesn't exist in the original text, the application layer throws an error.
*   **Bias:** Guarded by an anonymization step that strips company names and demographic identifiers before the text reaches the AI. 
*   **Cost/Latency:** Guarded by failing proposals at the rule-based "Mandatory Check" stage. If a proposal lacks basic licensing, it is immediately marked ineligible, saving the cost of sending it to the more expensive AI Scoring step.
