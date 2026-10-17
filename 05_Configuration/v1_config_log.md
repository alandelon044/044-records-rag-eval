# Version 1 configuration log

Date: October 16, 2026 (Europe/Kyiv).
Status: BUILT AND PUBLISHED. Setup acceptance is PARTIAL because Google intermittently returns HTTP 503. Four planned smoke questions received responses; the demo-submission question did not complete successfully after retries.

## Published app and baseline snapshot

- App: 044 Records Support — v1.
- Type: Chatflow.
- Live app: https://udify.app/chat/fVOybmn4pYiqwE40
- Editor: https://cloud.dify.ai/app/514f7f34-0792-4afe-80aa-1d32199e00ae/workflow
- Publication #2 is the v1 baseline. Publication #1 was the same behavior with localized Start/Answer node labels; #2 uses English node titles.
- Flow: Start → Knowledge Retrieval → LLM → Answer.
- Exact exported configuration: v1_workflow.yml.
- Web app language: English (United States).
- Web app description: Fictional portfolio demo: support for 044 Records fans and artists.

## Model

| Parameter | Recorded value |
| --- | --- |
| Provider | langgenius/gemini/google |
| Model ID | gemini-3.1-flash-lite |
| Dify display name | Gemini 3.1 Flash-Lite |
| Temperature | 0.2, confirmed in exported DSL |
| Gemini plugin | langgenius/gemini 0.9.11 |
| Thinking level | Minimal, provider default shown in UI |
| Output length | 65536, provider default shown in UI |
| Top P | No explicit override; disabled switch, displayed default 0.95 |
| Include thoughts | False, default |
| Grounding / URL context / code execution | False, defaults |
| Service tier | standard, default |
| Media resolution | Default |
| Vision | Disabled |
| JSON schema / stop sequences | Not configured |
| Failure retries | Disabled, default; setup retries were manual and recorded separately |

Only temperature is explicitly serialized in completion_params. Other model behavior follows the installed provider plugin defaults; the UI defaults above are observations, not explicit parameter overrides.

Gemini 3.1 Flash-Lite is listed with free input and output on the Google pricing page, checked October 16, 2026:
https://ai.google.dev/gemini-api/docs/pricing#gemini-3.1-flash-lite
Free-tier quotas and service availability still apply.

## Full system prompt

```text
You are a support assistant for 044 Records. Answer questions from fans and artists using the information in the context.

Context:
{{#context#}}
```

The special Context token was inserted through the Dify prompt editor. No explicit grounding-only, refusal, hallucination-prevention, or prompt-injection rules were added.

## Variables and features

- Retrieval query: Start.sys.query.
- LLM Context enabled: Knowledge Retrieval.result (node 1792154262076).
- Default user query template: sys.query, followed by two newlines and sys.files.
- Chat memory: default configuration present; window limiting disabled, stored window size 10 inactive.
- Answer output: LLM.text.
- Citations and Attributions: enabled, confirmed in UI and DSL (retriever_resource.enabled: true).
- Opening statement, follow-up suggestions, moderation feature, speech features, and file uploads: disabled/default.
- Conversation variables and environment variables: empty.

## Knowledge base

- Name: 044 Records Knowledge Base — v1.
- ID: 1f68ef90-ee52-47e8-a5c7-de5cacd6ce47.
- Exactly 10 Markdown documents uploaded from 01_Knowledge_Base.
- Every document was observed with Available status.
- Excluded: 02_Test_Cases/00_gaps_NOT_in_knowledge_base.md.
- Source hashes: v1_source_manifest.csv.
- Frozen source copies: v1_knowledge_sources.zip.

| Setting | Recorded value |
| --- | --- |
| Chunk mode | General |
| Separator | Literal escaped newline: \n |
| Maximum chunk length | 1024 characters |
| Chunk overlap | 50 characters |
| Replace consecutive spaces, newlines and tabs | Enabled |
| Remove URLs and email addresses | Disabled |
| Index method | High Quality |
| Embedding model | gemini-embedding-001, Gemini provider |
| Search | Hybrid Search |
| Reranking strategy | Reranking model, qwen3-rerank |
| Reranker provider | langgenius/tongyi/tongyi; Tongyi plugin 0.2.23 |
| Knowledge-base Top K | 3, default |
| Knowledge-base score threshold | Disabled; displayed inactive value 0.5 |
| Hybrid weights | Not used in reranking-model mode |

The actual default separator produced short, line-level chunks. For example, 01_about_044_records.md has 19 chunks, average length 64 characters. This was observed and preserved without tuning.

## Effective Chatflow retrieval settings

The retrieval node has its own defaults. These must be recorded separately from the knowledge-base settings:

- Retrieval mode: multiple.
- One selected knowledge base: 044 Records Knowledge Base — v1.
- Reranking enabled: true.
- Reranking mode: reranking_model.
- Reranker: qwen3-rerank through langgenius/tongyi/tongyi.
- Node Top K: 4, default. This differs from the knowledge-base Top K of 3.
- Node score threshold: disabled; UI displayed inactive value 0.7.
- Metadata filtering: disabled.
- Weights: not used.

The Tongyi reranker is a separate provider dependency from Gemini. No new Tongyi credentials were created. Its workspace quota must remain available for the formal evaluation.

## Setup decisions and chronology

1. Created the blank Chatflow and entered a short baseline prompt.
2. Initially selected Gemini 2.5 Flash-Lite at temperature 0.2. The first smoke request returned 404: the model was no longer available to new users. This was a setup failure, not a model-quality result.
3. Inspected Gemini 3.5 Flash-Lite. Its Dify parameter UI did not expose temperature, so it was not retained.
4. Selected stable Gemini 3.1 Flash-Lite, which exposes temperature. Set 0.2 and verified the saved value in the exported DSL. No formal evaluation had begun.
5. The user enabled the Chrome extension file-URL permission after the upload tool reported that it was required.
6. Uploaded all 10 files individually because the Sandbox UI allows one file at a time. Preserved the observed chunking and search defaults, apart from the explicitly requested Gemini embedding and Hybrid Search selections.
7. Connected retrieval output to LLM Context, inserted the Context token, and confirmed citations.
8. Performed three retrieval checks and isolated smoke dialogues. Recorded all successful responses, provider failures, and one operator-cancelled request separately.
9. Published the app, set its client interface to English, and updated Start/Answer node titles to English. These presentation changes did not change model behavior.
10. Exported the saved workflow, archived source documents, and captured screenshots. The download completed even though the browser download-event wait timed out.

## Verification and limitations

- All 10 documents: Available.
- Retrieval checks: 3/3 returned relevant chunks. Raw results are in v1_retrieval_checks.json.
- Successful planned smoke questions: shipping cost, royalty threshold, missing gift-card information, and an English typo about hoodie price.
- Demo submission smoke question: repeated HTTP 503 UNAVAILABLE responses from Google, including in the published app.
- An additional published-app question about music genres also encountered HTTP 503; see v1_published_smoke_failure.txt and v1_provider_503.jpg.
- One typo-check attempt was reset before completion and is explicitly excluded; its fresh retry received an answer.
- Four successful responses are preserved verbatim in v1_smoke_checks.json. The readable record is v1_stage3_verification.md.
- Setup checks are not the formal test set and are not used to report accuracy or improvement.
- Full five-question setup acceptance remains open until provider availability allows the remaining check to finish. The app has demonstrated working retrieval, generation, and source display, but reliable live availability has not been established.

Do not change this model, prompt, knowledge source snapshot, or retrieval settings during the formal v1 run. Any later change requires a new configuration-log entry.

## Amendment 1 — knowledge base document 01 (October 16, 2026, before the formal v1 run)

Reason: the user reviewed the knowledge base and found that item 1.2 of 01_about_044_records.md ("The label releases electronic music and alternative pop...") partly repeated 1.1 and 2.1 and did not answer "Who we are". Duplicated facts risk future contradictions, which would be a knowledge-base error rather than an assistant error.

Change (one document only; no other document, model, prompt or retrieval setting changed):

- Removed old item 1.2.
- Old item 1.3 became 1.2 (the meaning of the name "044").
- Item 2.1 now reads: "Release electronic music and alternative pop by artists based in the United States, on streaming platforms and on vinyl."
- No facts were added or removed; the genre and the "artists based in the United States" statement were moved into 2.1.

Files:

- 01_Knowledge_Base/01_about_044_records.md: 1278 -> 1247 bytes, new SHA256 in v1_source_manifest.csv.
- v1_knowledge_sources.zip: rebuilt with the updated document.
- v1_knowledge_sources_before_doc01_edit.zip: the original snapshot, kept for audit.

Dify status: DONE (October 16, 2026, 18:18 Europe/Kyiv).

- The edited file was uploaded through Add file in the same knowledge base (044 Records Knowledge Base - v1). Dify updated the existing document 01_about_044_records.md in place: the knowledge base still has exactly 10 documents, status Available.
- The upload wizard showed the same settings as v1: General, separator \n, maximum chunk length 1024, overlap 50, replace consecutive spaces enabled, remove URLs/emails disabled, High Quality, gemini-embedding-001, Hybrid Search, reranking model qwen3-rerank, Top K 3, score threshold disabled. Nothing was changed in the wizard.
- Result: 18 chunks (was 19), average chunk length 66 characters. Chunk 03 = item 1.1, chunk 04 = new item 1.2 (name "044"), chunk 06 = new item 2.1 with the genre and "artists based in the United States".
- Credits: 175 before and after. Indexing uses the Gemini embedding key, not Dify credits.
- No other document, model, prompt or retrieval setting was changed. The formal v1 run may start from this snapshot; the v1 baseline is this configuration plus Amendment 1.

## Amendment 2 — reranker replaced with weighted score (October 17, 2026, 00:36 Europe/Kyiv)

Reason: Dify trial credits. The model-provider page showed Tongyi (qwen3-rerank) running on Dify AI credits, while Gemini runs on the user's own key and uses no credits. Credits fell to 70 of 200 (130 used) after about 11 test questions plus 503 retries and a few retrieval checks; one request alone cost 6 credits, not about 1 as planned. Requests that fail with Google 503 still spend credits, because retrieval and reranking run before the LLM fails. Finishing v1, v2 and v3 (about 120 requests) was not possible. An own Alibaba Cloud Model Studio key was attempted but not completed.

Change (retrieval re-ranking strategy only):

- Knowledge Retrieval node: Hybrid Search, re-ranking strategy Weighted score, semantic 0.7 / keyword 0.3 (Dify defaults, not tuned), Top K 4, score threshold disabled.
- Knowledge base retrieval settings: Hybrid Search, Weighted score, semantic 0.7 / keyword 0.3, Top K 3, score threshold disabled. Saved without re-indexing.
- Observation: in all retrieval checks so far the node returned 3 chunks, so the knowledge-base Top K of 3 is effectively the limit. Recorded as is; not changed.

Unchanged: model (Gemini 3.1 Flash-Lite, own key), temperature 0.2, prompt, documents (10 documents, 18 chunks, Amendment 1 included), chunk settings, embedding model (gemini-embedding-001), 503 retry rule, test questions, expected answers and rubric.

Consequences:

- The formal v1 run restarts from SF-01 with this configuration. All 40 questions are collected again.
- The 10 answers scored with qwen3-rerank (SF-01 to SF-10) and one unrecorded answer (CP-01) are kept on the "Pilot (excluded)" tab. They are not part of the v1-v3 results.
- The weights 0.7 / 0.3 are fixed before the new run and stay the same in v1, v2 and v3 unless a later amendment says otherwise.
- The 40 questions are a fixed set for iterative evaluation, not a blind test: the first answers were analysed in detail during the pilot.
- The remaining 70 credits are kept for an optional retrieval-only comparison (weighted score vs qwen3-rerank) on questions chosen in advance.

Dify status: settings changed by the user; app republished.

## Amendment 3 — workspace default models; correction of the credit cause (October 17, 2026, 00:55 Europe/Kyiv)

Finding: credits kept falling after Amendment 2 (70 to 25 in 9 requests, about 5 per request), although the published app (version #4, published right after Amendment 2) no longer used a reranker. The workspace "Default Model Settings" showed:

- System reasoning model: gpt-5 (OpenAI, paid with Dify AI credits).
- Default embedding model: text-embedding-3-small (OpenAI, Dify AI credits).
- Default rerank model: qwen3-rerank (Tongyi, Dify AI credits).

Dify uses the system reasoning model for workspace tasks such as generating a title for every new conversation. The test protocol opens a new conversation for every question, so each question triggered a gpt-5 call. This, not mainly the reranker, was the main source of credit use during the pilot and after Amendment 2. The cause stated in Amendment 2 was therefore incomplete; the reranker's own share was not measured.

Change (workspace defaults only; the app's LLM, prompt and retrieval settings are unchanged):

- System reasoning model: gpt-5 -> Gemini 3.1 Flash-Lite (own key).
- Default embedding model: text-embedding-3-small -> gemini-embedding-001 (own key). The knowledge base was already indexed with gemini-embedding-001 set explicitly, so nothing was re-indexed.
- Default rerank model: left as qwen3-rerank; the Knowledge Retrieval node uses weighted score and does not call it.

Check: after the change, a new question did not reduce the credit balance (25 before and after).

Decision: weighted score from Amendment 2 is kept. With 25 credits left, a reranker run for v1-v3 (about 120 requests) is still not possible, and changing the retrieval method again would restart v1 a second time. The 9 answers collected after Amendment 2 used the correct configuration (weighted score, published version #4) and stay in the new v1; conversation titles do not affect the answers.

Lesson for the README: check workspace-level default models, not only the models inside the app, before estimating cost.
