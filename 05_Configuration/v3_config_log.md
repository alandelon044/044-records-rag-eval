# v3 configuration log — retrieval only

Date: October 17, 2026, about 23:58 Europe/Kyiv (published in Dify by the user).

## What changed

Only the knowledge base chunking and the retrieval Top K. The prompt is the v2 prompt, unchanged.

| Setting | v1 / v2 | v3 |
|---|---|---|
| Knowledge base | 044 Records Knowledge Base — v1 | 044 Records Knowledge Base — v3 (new, same 10 files) |
| Chunk mode | General | Parent-child |
| Chunks used for search | one line each (separator \n, max 1024, overlap 50; about 65 characters on average) | child chunks: separator \n, max 512 characters |
| Context given to the model | the matched line only | the parent = the whole document (Full doc mode) |
| Top K, knowledge base | 3 | 4 |
| Top K, Knowledge Retrieval node | 4 (in practice 3 were returned) | 4 |

Parent-child details: the Dify summary shows "Hierarchical · Full document; parent 1024, child 512". In Full doc mode the parent is the whole document; the 1024 value is not applied (the FAQ parent is 2446 characters, document 01 is 1228 characters, both complete). Top K counts child chunks; the matched children are grouped by parent document, so 2–4 documents reach the model.

Unchanged: model Gemini 3.1 Flash-Lite (own key), temperature 0.2, v2 SYSTEM prompt, Hybrid Search, weighted score semantic 0.7 / keyword 0.3, score threshold disabled, embedding model gemini-embedding-001 (High Quality), text preprocessing (replace consecutive spaces on, remove URLs/emails off), the same 10 source files (Amendment 1 included), test questions, key facts, rubric, 503 retry rule. The v1 knowledge base is kept unchanged for reproducibility.

Indexing used the user's Gemini key; Dify credits stayed at 25. Gemini API quota is used for embeddings.

## Why (evidence from v1/v2)

Causes of the v2 errors were checked in Retrieval Testing on the v1 knowledge base before this change: 16 of 20 v2 errors are search errors (the needed chunk was not retrieved), 4 are generation errors. Patterns seen in the checks:

- FAQ question-only chunks ("...?**" without the answer) took a retrieval slot in 8 of 13 checks (SF-07, CP-03, CP-04, EX-01, EX-06, EX-07, EX-08, FP-03); bare headings ("Shipping", "Returns and Refunds", "1. Buying tickets", "5. Changing or canceling an order") in 4. (Corrected on October 18, 2026: an earlier draft said 10 of 12.)
- Duplicate facts (FAQ and the primary document with the same sentence) took two of the three slots.
- Neighbouring items of one section were split apart (support hours 01 4.2 vs reply time 01 4.3; pre-order limit 06 2.1 vs refund of extra copies 06 2.3).

## Diagnostic checks before the run (Retrieval Testing, knowledge base v3)

| Query | Retrieved parents | Result |
|---|---|---|
| SF-09 pre-order 5 copies | 10_faq (2446 chars), 06_preorders (1021), 02_store (1013); 4 child hits | 06_preorders is retrieved, including 2.3 (extra copies canceled and refunded) |
| SF-02 answer at 3 am | 03_shipping, 04_payments, 01_about (1228, includes support hours 4.2), 08_demo | Whole document 01 reaches the model, although the matched child lines are still not the best ones (04 5.2, "Delivery time") |
| FP-03 ship to Iceland | 10_faq, 03_shipping (839, includes 1. Where we ship) | Shipping area rules are in the context |

Preview check (not part of the run): SF-09 answer now includes "the extra copies will be canceled and refunded", citing 06_preorders.

FAQ child chunks now contain the question and its answer together (for example "How much is shipping? Standard shipping is $6 per order..."), unlike v1 where they were split.

## Hypotheses (written before the v3 run; predictions, not results)

v2 baseline: 20/40 fully correct (50%), average correctness 1.48, behavior pass 82.5%; 16 search errors, 4 generation errors.

Expected to improve, if the whole parent document reaches the model:
- Search errors where the missing fact sits in a document that is now retrieved: SF-02 (support hours), SF-07 (receipt within 3 business days), SF-09 (extra copies refunded), CP-01 (genre), CP-02 (shipping month, Chicago delivery), CP-03 (France, returns), CP-04 (how to cancel, reply time), CP-05 (payment methods), EX-01, EX-04, EX-06, EX-07, EX-08 (exceptions and procedures), FP-01, FP-03.
- Fewer "I don't have that information" answers (OVER_REFUSAL).

Not expected to improve (the prompt is unchanged):
- Prompt side effects from v2: SF-01 (other payment methods dropped), SF-05 (hedging on crypto), FP-04 (exceptions dropped), "That is not correct" without a false premise.
- SF-03 (demo rule mixed into the genre answer) is a generation error and may remain or get worse with more context.

Risks:
- More text per answer: the model may add rules that do not apply, or still leave out details (generation errors).
- A needed document can still miss the top 4 child hits if other documents match the question words better.

v3 tests the combined retrieval change (parent-child Full doc chunking plus Top K 4). An improvement can be attributed to this set of changes, not to parent-child or Top K separately.

## Run

Results go to the "Run v3" tab of TestSet_working.xlsx: same 40 questions, a new conversation for each, same 503 retry rule. Causes of errors are confirmed with Retrieval Testing on knowledge base v3.
