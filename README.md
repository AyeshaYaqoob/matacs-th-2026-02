
# AI Proposal Triage Tool Design

**Name:** Ayesha Yaqoob
**Reference:** MATACS-TH-2026-02  
**Time Spent:** ~60 minutes  

## AI Usage Statement
I used AI (Gemini) as a thought partner to help structure the write-up and draft the JSON schema. **Where I disagreed:** The AI initially suggested doing all the scoring and final percentage weighting directly inside a single LLM prompt. I disagreed and separated the architecture into an "AI Extraction" step and a "Rule-Based Math" step. LLMs are notoriously unreliable at arithmetic and strict binary logic, so keeping the math in plain code ensures accurate, deterministic final scores.

## Repository Contents
*   [`diagram/process_flow.md`](diagram/process_flow.md) - Description of the visual process flow (or upload your image here).
*   [`WRITEUP.md`](WRITEUP.md) - Answers to the architecture and risk questions.
*   [`PROMPTS.md`](PROMPTS.md) - The specific prompts used for the AI system.
*   [`ranking.json`](ranking.json) - The final evaluation output of the sample proposals.
