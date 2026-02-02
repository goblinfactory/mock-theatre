# 06. Testing pyramid and test types

## Objective

Ground the discussion in test types and where they fit, so teams can choose the
right level of test for the risk they are managing.

## Key points

- The pyramid is a balance: many fast tests, fewer slow tests.
- Unit tests are for logic and fast feedback, not for wiring.
- Component tests validate a service in isolation with real internals.
- Integration tests verify wiring to real infrastructure.
- Functional tests validate user-facing behavior, often end-to-end.

## Testing diamond (fat middle)

- Some teams use a testing diamond: fewer unit tests, a heavier middle of
  service or API tests, and fewer UI end-to-end tests.
- BDD frameworks like Reqnroll and SpecFlow often sit in that middle layer to
  express behavior with readable scenarios.
- The tradeoff: more realistic coverage, but slower feedback and higher
  maintenance if the middle grows too large.

## Quick demo (one behavior, different levels)

Behavior: "Order total includes tax."

Unit test (pure calculation):

```csharp
Assert.Equal(108m, TaxCalculator.TotalWithTax(100m, 0.08m));
```

Component test (service with real collaborators, no external I/O):

```csharp
var taxTable = new InMemoryTaxTable(new Dictionary<string, decimal>
{
    ["CA"] = 0.08m
});
var service = new OrderService(new TaxCalculator(taxTable));

Assert.Equal(108m, service.Total("CA", 100m));
```

Integration test (real DB or config):

```csharp
using var db = new RealDbConnection(connectionString);
var service = new OrderService(new TaxCalculator(new TaxTable(db)));

Assert.Equal(108m, service.Total("CA", 100m));
```

Functional test (black-box API):

```csharp
var response = await client.PostAsJsonAsync("/orders/total",
    new { state = "CA", subtotal = 100m });
response.EnsureSuccessStatusCode();
Assert.Equal(108m, await response.Content.ReadFromJsonAsync<decimal>());
```

## 2-minute prompt

"Where is your team's biggest testing gap on the pyramid or diamond, and why?"

## Facilitation note (invite SMEs)

A SME share on how they decide whether a test should be unit, component,
integration, or functional for a given change.

[Back to index](index.md)
