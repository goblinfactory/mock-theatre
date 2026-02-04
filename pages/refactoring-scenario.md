---
title: Refactoring Scenario
---

Change: replace `FixedDiscountPolicy` with `TieredDiscountPolicy`

What breaks?
- Chicago tests stay green if invoice totals stay correct
- London tests may break if the collaborator call graph changes
- Both are acceptable trade-offs if you choose deliberately

Note: interaction-based tests are more coupled to the SUT's implementation
