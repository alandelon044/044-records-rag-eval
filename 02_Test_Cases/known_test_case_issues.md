# Known test-case issues (found after the runs)

Found on October 17, 2026, while reviewing the scored v1 and v2 answers, after both runs were finished. Updated on October 18, 2026, after the v3 run.

**No scores, key facts or expected behaviors were changed because of this review.** All versions are scored with the same strict rule: an answer that omits any listed key fact gets 1, not 2. The list below documents where that rule is stricter than the question requires, so the results can be read with this in mind.

## 1. Key facts that go beyond what the question asks

In these cases the only missing item was a fact that the question did not directly ask for, and that omission alone cost the point.

| ID | Item in the key facts that the question does not need | Where it cost a point |
|---|---|---|
| SF-01 | Other payment methods (cards, Apple Pay, Google Pay, Afterpay); the question asks only about PayPal | v2, v3 |
| SF-09 | "Extra copies are canceled and refunded"; the question asks only whether 5 copies are allowed | v1, v2 |
| CP-05 | List of accepted payment methods; the question asks only about Venmo | v1 (v2 also lost the point for a different reason) |
| EX-01 | The damaged/wrong-item exception with 48 hours and photos; the question is about a removed tag | v1, v2 |
| EX-05 | "A statement is still issued every quarter"; the question asks only about payment | v1 |
| EX-06 | Procedure and outcome of the damaged-item exception (48 hours, photos, replacement or refund) | v1, v2 |
| EX-07 | "The ticket stays valid for the new date"; the question asks only about a refund | v1, v2 |
| EX-08 | "Report within 48 hours with photos" | v1, v2 |
| FP-01 | "E-tickets arrive by email" (in v1 and v2 the answer also lacked "only on our website", so the point may have been lost anyway; in v3 this was the only missing item) | v1, v2, v3 |
| FP-04 | The two exceptions (label cancels or postpones); the question is about the 3-day claim | v2, v3 |
| CP-03 | "Afterpay is accepted but does not change this"; the customer did not ask about payment | v3 |
| CP-04 | Support hours (Mon-Fri 9-5 CT); the question asks how to cancel and how soon support replies (2 business days) | v3 |

Borderline, kept as a valid key fact:

- EX-02, "a valid photo ID is required": relevant to whether a 19-year-old can get in. Cost the point in v1 only.

Effect on the comparison: the strict rule lowers all versions. Some items penalize several versions equally, some only v1 (EX-05, EX-02), some only v2 and v3 (SF-01, FP-04), some only v3 (CP-03, CP-04). The direction is mixed; neither the v1 to v2 change (13 to 20 fully correct) nor the v2 to v3 change (20 to 28) depends on any single item.

## 2. Scoring inconsistency

- SF-06, v1: scored 2 although the answer only confirmed XXL and did not give the size range S to XXL from the key facts. Under the strict rule this would be 1. The v2 answer listed all sizes and is a clean 2. The v3 answer again confirmed only XXL (plus measurements) and was scored 1 under the strict rule. Kept as scored; the v1 score is more lenient than the rule applied elsewhere, which slightly favors v1 in the comparison.

## 3. Category design

- NK-01 ("What is MALA 044's real name?"), category Not in KB: the real name is indeed not in the knowledge base, but 01_about_044_records item 3.2 says the label does not share artists' personal contact details. The ideal answer could refer to this rule; the key facts did not require it. Both versions scored 2. Found by the author during scoring.

## 4. Key-fact items that did not change any score

- CP-03, v1 and v2: "Afterpay is accepted but does not change this" and the full return conditions. The answers lost the point on the France shipping part anyway. (In v3 the Afterpay item did cost the point; see section 1.)
- CP-06: return conditions (14 days, unused, tags). The answers lost the point on the cost part anyway.

## How this is reported

README wording: "Strict scoring was kept identical for all versions: an answer that omits any listed key fact gets 1, not 2. Test-case issues found after the runs are listed in known_test_case_issues.md; no scores were changed."
