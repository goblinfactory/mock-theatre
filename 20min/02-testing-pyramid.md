# 02. Testing pyramid and test types

## Objective

Place each test type in context so the team can choose the right level of test.

## Key points

- Unit tests: small, fast, logic-focused.
- Component tests: a service in isolation with real internals.
- Integration tests: real infrastructure and wiring.
- Functional tests: user-visible behavior, often end-to-end.
- Some teams use a testing diamond (fat middle) with BDD tools like Reqnroll or
  SpecFlow sitting in the service/API layer.

## Quick demo (one behavior, two levels)

```csharp
Assert.Equal(108m, TaxCalculator.TotalWithTax(100m, 0.08m));
```

```csharp
var response = await client.PostAsJsonAsync("/orders/total",
    new { state = "CA", subtotal = 100m });
Assert.Equal(108m, await response.Content.ReadFromJsonAsync<decimal>());
```

## 2-minute prompt (optional)

"Where is your biggest testing gap on the pyramid or diamond?"

## Facilitation note (invite SMEs)

A SME heuristic for choosing test type under time pressure can help.

[Back to index](index.md)
