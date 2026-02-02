# 02. Chicago vs London TDD

## Objective

Compare classical (Chicago) and mockist (London) TDD so the group can choose
deliberately, not by habit.

## Key points

- Chicago style favors real collaborators and state-based assertions.
- London style favors mocks and interaction-based assertions.
- Both are valid; the risk is using London-style mocks everywhere.
- The choice often depends on dependency cost and observability.

## Quick demo (two tiny tests)

Chicago-style test (real collaborator, state-based):

```csharp
var taxTable = new InMemoryTaxTable(new Dictionary<string, decimal>
{
    ["CA"] = 0.08m
});
var calculator = new TaxCalculator(taxTable);
var service = new OrderService(calculator);

var total = service.Total("CA", 100m);

Assert.Equal(108m, total);
```

London-style test (mocked collaborator, interaction-based):

```csharp
var tax = new Mock<ITaxCalculator>();
tax.Setup(t => t.Calculate("CA", 100m)).Returns(8m);
var service = new OrderService(tax.Object);

var total = service.Total("CA", 100m);

Assert.Equal(108m, total);
tax.Verify(t => t.Calculate("CA", 100m), Times.Once);
```

## 2-minute prompt

"Where do you draw the line between real collaborators and mocks in your codebase?"

## Facilitation note (invite SMEs)

Invite an engineer who has used both styles to share one situation where each
style worked well.

[Back to index](index.md)
