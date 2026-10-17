# 044 Records — evaluating and improving a RAG support assistant

A customer-support assistant for a fictional U.S. music label, built in Dify and evaluated on 40 test questions in three versions. Each version changed one part of the system: **fully correct answers went from 13/40 (32.5%) to 28/40 (70%)**, and every error was traced to either retrieval or generation.

## Goal

Find out not only *whether* a RAG assistant answers well, but *why* it fails, and check whether a targeted change fixes the failures it was meant to fix.

## Setup

- **Assistant:** Dify chatflow `Start → Knowledge Retrieval → LLM → Answer`, Gemini 3.1 Flash-Lite, temperature 0.2.
- **Knowledge base:** 10 short English policy documents written for the project (store, shipping, payments, returns, pre-orders, events, demos, royalties, FAQ). Some topics were left out on purpose to test refusals.
- **Live demo:** [044 Records Support](https://udify.app/chat/fVOybmn4pYiqwE40), the current v3 configuration (fictional label, English only).
- **Test set:** 40 questions in 6 categories: simple fact (10), compound (6), exception to a rule (8), not in the knowledge base (8), false premise (4), off-topic or provocation (4). Six questions are written casually (typos, abbreviations, or missing punctuation); the planned range was 4–5.

## How answers were scored

- **Key facts were written before the first run** and never changed after scoring.
- Each answer gets **Correctness 0–2**, **Behavior Pass/Fail** (answer, refuse, correct the premise, or redirect), and **Source Pass/Fail**.
- **Strict rule, identical for all versions:** an answer that omits any listed key fact gets 1, not 2. Saying "I don't have that information" when the answer exists in the knowledge base is a Behavior Fail.
- **Every error has a cause:** `search` (the needed text was not retrieved) or `generation` (it was retrieved, but the model got it wrong). Causes were confirmed with Dify Retrieval Testing, not guessed from citations.
- Every question was asked in a new conversation. Rubric: `TestSet.xlsx`, tab *Rubric*.

## Versions

| Version | What changed | Why |
|---|---|---|
| v1 | Baseline: one-line prompt, line-by-line chunks, hybrid search (weighted score), Top K 3 | Starting point |
| v2 | **Prompt only:** 8 rules (use only the context, say "I don't know" and point to support, correct wrong assumptions, answer every part, speak as support staff) | v1 invented answers and accepted false premises |
| v3 | **Retrieval only:** parent-child chunking (search on single lines, give the model the whole document), Top K 4 | 16 of 20 v2 errors were retrieval errors |

Predictions for v2 and v3 were written in the config logs before each run.

## Results

| | v1 | v2 | v3 |
|---|---|---|---|
| **Fully correct** | 13/40 (32.5%) | 20/40 (50%) | **28/40 (70%)** |
| Average correctness (0–2) | 1.18 | 1.48 | 1.70 |
| Behavior pass | 82.5% | 82.5% | 95% |
| Source pass (where expected) | 75% | 75% | 92.9% |
| Errors caused by search | 16 | 16 | **2** |
| Errors caused by generation | 11 | 4 | 10 |

| Category (fully correct) | v1 | v2 | v3 |
|---|---|---|---|
| Simple fact | 60% | 40% | 70% |
| Compound | 0% | 0% | 17% |
| Exception | 12.5% | 37.5% | 75% |
| Not in knowledge base | 12.5% | 100% | 100% |
| False premise | 50% | 25% | 50% |
| Off-topic / provocation | 75% | 100% | 100% |

## Three errors that show the story

**1. False premise — "You ship to Iceland, right?"** (FP-03)
- v1: *"Yes, we ship to Iceland!"* — invented; no shipping-area text was even retrieved.
- v2: *"I don't have information regarding whether we ship to Iceland."* — the prompt stopped the invention, but retrieval still missed the rule.
- v3: *"That is not correct. Shipping is available only within the contiguous United States."* — fixed once the whole shipping document reached the model.

**2. Not in the knowledge base — discounts and promo codes** (NK-08)
- v1: *"Currently, we do not have specific discounts… or active promo codes"* plus a wrong limit "2 copies per item" — a confident guess presented as policy.
- v2 and v3: *"I don't have that information. Please contact support."* The prompt fixed all 8 questions of this category.

**3. A prompt fix with a side effect** (v2)
- v2 removed invented answers (`NO_REFUSAL` 4 → 0) but created false "I don't know" answers (`OVER_REFUSAL` 1 → 7), for example on a crypto-payout question whose answer was in the retrieved text. Behavior pass stayed at 82.5%: one error type was traded for a safer one.

## Conclusions

- **Measure the cause, not only the score.** After v2, 80% of the remaining errors came from retrieval, which no prompt can fix. The combined v3 retrieval change (Parent-child chunking plus Top K 4) reduced retrieval errors from 16 to 2.
- **Prompt and retrieval fix different things.** The v2 prompt eliminated `NO_REFUSAL` guesses and improved off-topic handling. It corrected FP-01, but false-premise performance fell from 50% in v1 to 25% in v2; v3 brought it back to 50% after the shipping-area rule reached the model in FP-03.
- **Short chunks hurt in a small, structured knowledge base.** FAQ questions were split from their answers and took one of the three retrieval slots in 8 of 13 checked queries.
- **The bottleneck is now generation.** 10 of 12 remaining v3 errors had the needed text in the context. Half are side effects of the v2 prompt (dropped details, over-cautious hedging, "That is not correct" without a false premise). That is the next thing to fix.

## Limitations

- **Not a blind test:** the same 40 questions were used for all versions, and early answers were analysed in detail. It is a fixed set for iterative evaluation.
- **Casual-style target:** the plan called for 4–5 casual questions, but the final set contained 6. This was detected after the runs; style labels did not affect scoring.
- **One scorer** (the author), no second reviewer; scoring is strict, and some key facts are broader than the question needs. Issues found after the runs are listed in [`known_test_case_issues.md`](02_Test_Cases/known_test_case_issues.md); no scores were changed.
- **v3 changed two retrieval settings at once** (chunking and Top K), so the gain belongs to the combination.
- **Small, clean knowledge base** (10 short documents). Giving the model whole documents works here; a large knowledge base would need section-level parents.
- **One run per question** at temperature 0.2; answers can vary between runs.
- **Mid-project configuration change:** the reranker was replaced with weighted scoring after the free Dify credits ran out, and v1 was re-run from scratch. The first 10 answers are kept on the *Pilot (excluded)* tab and are not part of the results.

## Repository

| Path | Contents |
|---|---|
| `01_Knowledge_Base/` | The 10 knowledge-base documents |
| `02_Test_Cases/TestSet.xlsx` | Test set, rubric, all answers and scores for v1–v3, summary, pilot run |
| `02_Test_Cases/known_test_case_issues.md` | Test-case issues found after the runs |
| `02_Test_Cases/00_gaps_NOT_in_knowledge_base.md` | Topics deliberately left out of the knowledge base |
| `05_Configuration/v1_config_log.md`, `v2_…`, `v3_…` | Exact settings, every change with its reason, predictions written before each run |
| `03_Screenshots/` | Dify setup screenshots |
| `04_Reports/per_question_results.md` | Score, error tag and cause for every question in v1, v2 and v3 |
| `05_Configuration/setup_notes.md` | Notes from the initial setup (stages 0–3) |

## Authorship

The test categories and the questions are mine; Claude (AI assistant) helped with English phrasing and proofreading, and suggested a few questions that I reviewed and approved. The rubric and the final decision on every score are mine. Claude also helped draft the knowledge base and the configuration logs.
