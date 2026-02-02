# 03. When tests lie

## Objective

Spot the symptoms of green tests that do not protect real behavior.

## Key points

- Interaction-only tests can pass while the user-visible outcome is broken.
- Over-mocking hides integration mistakes and configuration errors.
- Tests that assert only "calls happened" are easy to satisfy accidentally.
- A suite can be 100% green and still allow regressions in behavior.

## Quick demo (failure mode)

Example test that does not assert the outcome:

```csharp
var inventory = new Mock<IInventory>();
var service = new OrderPlacer(inventory.Object);

service.Place(order);

inventory.Verify(i => i.Reserve(order.Id), Times.Once);
```

Note that this passes even if `Place` returns the wrong result, the confirmation
email is never sent, or the wrong order ID is passed in.

## 2-minute prompt

"What is one test in your system that would still pass even if a user-visible
feature broke?"

## Facilitation note (invite SMEs)

A SME story about the last regression that slipped through green tests can
highlight what the tests were missing.

[Back to index](index.md)
