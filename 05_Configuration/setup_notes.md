# Setup notes (stages 0–3)

Moved from the first README on October 18, 2026. Kept as a record of the initial setup; the current project description is in the main README.md.

## Stage 0 setup

- Portfolio project folder created.
- Gemini API key created in Google AI Studio and connected to the Gemini provider in Dify.
- Dify credential label: `044 Records Gemini`.
- The API key is stored in Dify and is not included in this project folder.

## Project folders

- `01_Knowledge_Base` — source documents for the assistant.
- `02_Test_Cases` — evaluation prompts and expected behavior.
- `03_Screenshots` — setup and test evidence. Never capture secret keys.
- `04_Reports` — findings, metrics, and final report.
- `05_Configuration` — non-secret configuration notes and prompts.

## Security note

Do not save API keys in project files or screenshots. If a key is exposed, revoke it in Google AI Studio and replace it in Dify.

## Stage 3 — baseline assistant

Built and published on October 16, 2026.

- Demo: https://udify.app/chat/fVOybmn4pYiqwE40
- Model: Gemini 3.1 Flash-Lite; temperature: 0.2.
- Knowledge: 10 English Markdown files, General chunks, High Quality Gemini embeddings, Hybrid Search.
- Short baseline prompt, retrieval context connected, source display enabled.
- Configuration: [v1_config_log.md](05_Configuration/v1_config_log.md).
- Setup verification: [v1_stage3_verification.md](05_Configuration/v1_stage3_verification.md).
- Workflow export: [v1_workflow.yml](05_Configuration/v1_workflow.yml).
- Screenshots: 03_Screenshots.

Setup acceptance is PARTIAL: four planned smoke questions received responses; the remaining demo-submission check and an additional published-app check encountered repeated Google HTTP 503 errors. Provider availability must recover before the remaining check can be accepted. Formal evaluation has not started.
