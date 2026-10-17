# Stage 3 verification record

Date: October 16, 2026 (Europe/Kyiv).
Result: assistant built and published; setup acceptance PARTIAL due intermittent Google HTTP 503 errors.
Live app: https://udify.app/chat/fVOybmn4pYiqwE40

These are unscored setup checks. They do not constitute the formal v1 evaluation. Each question was submitted in a new/reset conversation.

## S1 — Shipping

Question: How much is shipping on a $28 T-shirt order?

Exact response:
> Shipping for a $28 order is $6.

Sources displayed: 10_faq.md, 03_shipping.md, 02_store_and_catalog.md.
Result: response received with source display.

## S2 — Demo submission

Question: Can I submit my demo by email?

Result: no completed answer. Google returned HTTP 503 UNAVAILABLE on repeated Preview attempts and a published-app attempt. These are infrastructure errors and are not scored as hallucinations or wrong answers.

## S3 — Royalties

Question: When does an artist qualify for a royalty payment?

Exact response:
> An artist qualifies for a royalty payment when their account balance reaches at least $100. If the balance is less than $100, it will roll over to the next quarter.

Sources displayed: 10_faq.md, 09_artist_royalties.md.
Result: response received with source display.

## S4 — Missing information

Question: Do you sell gift cards?

Exact response:
> I'm sorry, but I don't have information regarding gift cards in our current records. If you have any other questions about our payment policies or orders, feel free to ask!

Sources displayed: 02_store_and_catalog.md, 04_payments_and_orders.md.
Result: response received. The assistant admitted missing information without an explicit refusal instruction in the baseline prompt. Source badges show retrieved documents; their presence alone does not establish support for every claim.

## S5 — Informal English with a typo

Question: how much is a hoddie

Exact response:
> A hoodie costs $55.

Sources displayed: 02_store_and_catalog.md, 04_payments_and_orders.md.
Result: response received. An earlier attempt was reset before completion; only the completed fresh retry is recorded as a response.

## Additional published-app check

Question: What kinds of music does 044 Records release?

Result: a fresh attempt returned HTTP 503 UNAVAILABLE. A preceding attempt was interrupted during browser recovery, so its final server outcome was not confirmed. The final visible failure is preserved in v1_published_smoke_failure.txt.

## Retrieval checks

1. Shipping under $75: relevant FAQ, catalog and shipping chunks returned (top score 0.87).
2. Damaged preorder vinyl: relevant exception chunks returned (top score 0.96).
3. Royalty payment threshold: relevant royalty and FAQ chunks returned (top score 0.94).

No retrieval or prompt tuning was made in response to these checks.

## Open acceptance item

Repeat S2 in a new conversation when Google service availability recovers. Preserve any provider errors and retries. The baseline model and settings must stay the same. Do not claim that all five planned smoke questions succeeded or that live availability was fully verified.
