---
title: Smells and Failure Modes
---

- Overspecified expectations that break on harmless refactors
- Tests that assert on private sequencing rather than outcomes
- Mocking everything, making failures noisy and brittle
- State-based tests that require adding test-only getters
- Interactions pass while outputs are still wrong

Source: https://blog.ploeh.dk/2019/02/18/from-interaction-based-to-state-based-testing/
