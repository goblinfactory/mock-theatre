---
theme: default
colorSchema: 'dark'
title: "Mock Theatre: when green tests mean nothing"
info: |
  Unconference facilitation deck
  Format: complete end-to-end, skip as needed
---

<style>
.slidev-layout {
  color: #e0e0e0; /* lighter gray instead of pure white */
}

/* or for specific elements */
h1, h2, h3 {
  color: #05f5f5;
}
</style>

# Mock Theatre

## when green tests mean nothing

<img src="/img/mock-theatre.png" class="h-80 mx-auto" />

---

# Mock Theatre

- Mock theatre: tests that verify the script, not the outcome
- Green tests can lie about real behavior
- Chicago vs London TDD is a deliberate choice
- Test levels should match the risk
- Useless tests discourage safe refactoring

---

# Mock theatre: definition

- Tests verify interactions instead of outcomes
- Implementation details get locked in
- Behavior can drift while the suite stays green

---

# Why green tests lie

- Interaction-only tests can pass while behavior is broken
- Over-mocking hides wiring and config errors
- A suite can be 100% green and still allow regressions

---

# The cost

- Slower change
- Brittle tests
- False confidence

---

# Over-specified interaction test

```csharp
public interface IEmailSender
{
    void Send(string to, string subject, string body);
}

[Fact]
public void Sends_welcome_email()
{
    var sender = new Mock<IEmailSender>();
    var service = new OnboardingService(sender.Object);

    service.Welcome("alice@example.com");

    sender.Verify(s => s.Send(
        "alice@example.com",
        "Welcome",
        "Hello Alice"), Times.Once);
}
```

Note: changing the email wording breaks the test even if the user still
gets the email and the business rule is satisfied.

---

# Prompt

Where have you seen green tests give false confidence?

<!--
Facilitation note: A quick share from someone who has maintained a fragile
test suite can add one example and how they recognized it.
-->

---

# Chicago vs London TDD

- Chicago: real collaborators, state-based assertions
- London: mocks, interaction-based assertions
- Both are valid; the risk is defaulting to mocks everywhere
- Choice depends on dependency cost and observability

---

# Chicago-style example

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

---

# London-style example

```csharp
var tax = new Mock<ITaxCalculator>();
tax.Setup(t => t.Calculate("CA", 100m)).Returns(8m);
var service = new OrderService(tax.Object);

var total = service.Total("CA", 100m);

Assert.Equal(108m, total);
tax.Verify(t => t.Calculate("CA", 100m), Times.Once);
```

---

# Prompt

Where do you draw the line between real collaborators and mocks in your codebase?

<!--
Facilitation note: A brief share from an engineer who has used both styles can
highlight one situation where each style worked well.
-->

---

# Testing pyramid and test types

- Unit tests: small, fast, logic-focused
- Component tests: service in isolation with real internals
- Integration tests: real infrastructure and wiring
- Functional tests: user-visible behavior, often end-to-end

---

# Testing diamond (fat middle)

- Some teams use fewer unit tests and a heavier middle
- BDD tools (Reqnroll, SpecFlow) often live in the service/API layer
- Tradeoff: more realistic coverage, slower feedback, higher maintenance

---

# Same behavior, different levels (1/2)

Behavior: "Order total includes tax."

Unit test:

```csharp
Assert.Equal(108m, TaxCalculator.TotalWithTax(100m, 0.08m));
```

Component test:

```csharp
var taxTable = new InMemoryTaxTable(new Dictionary<string, decimal>
{
    ["CA"] = 0.08m
});
var service = new OrderService(new TaxCalculator(taxTable));

Assert.Equal(108m, service.Total("CA", 100m));
```

---

# Same behavior, different levels (2/2)

Integration test:

```csharp
using var db = new RealDbConnection(connectionString);
var service = new OrderService(new TaxCalculator(new TaxTable(db)));

Assert.Equal(108m, service.Total("CA", 100m));
```

Functional test:

```csharp
var response = await client.PostAsJsonAsync("/orders/total",
    new { state = "CA", subtotal = 100m });
response.EnsureSuccessStatusCode();
Assert.Equal(108m, await response.Content.ReadFromJsonAsync<decimal>());
```

---

# Prompt

Where is your team's biggest testing gap on the pyramid or diamond, and why?

<!--
Facilitation note: A SME share on how they decide whether a test should be unit,
component, integration, or functional for a given change.
-->

---

# When tests lie

- Interaction-only tests can pass while outcomes are broken
- Over-mocking hides integration mistakes and config errors
- "Calls happened" assertions are easy to satisfy accidentally

---

# Failure mode example

```csharp
var inventory = new Mock<IInventory>();
var service = new OrderPlacer(inventory.Object);

service.Place(order);

inventory.Verify(i => i.Reserve(order.Id), Times.Once);
```

Note: this passes even if `Place` returns the wrong result, the confirmation
email is never sent, or the wrong order ID is passed in.

---

# Prompt

What is one test in your system that would still pass even if a user-visible
feature broke?

<!--
Facilitation note: A SME story about the last regression that slipped through
green tests can highlight what the tests were missing.
-->

---

# Useless test anti-pattern

- Asserting implementation text is mock theatre at its worst
- It tells you nothing about behavior or correctness
- It actively discourages refactoring, even safe refactoring

---

# The bad test

```csharp
[Fact]
public void FooFile_contains_exactly_these_25_lines()
{
    var expected = new[]
    {
        "using System;",
        "",
        "namespace Demo",
        "{",
        "    public class Foo",
        "    {",
        "        public int Add(int a, int b)",
        "        {",
        "            return a + b;",
        "        }",
        "",
        "        public int Multiply(int a, int b)",
        "        {",
        "            return a * b;",
        "        }",
        "    }",
        "}",
        "",
        "// line 19",
        "// line 20",
        "// line 21",
        "// line 22",
        "// line 23",
        "// line 24",
        "// line 25"
    };

    var actual = File.ReadAllLines("Foo.cs");

    Assert.Equal(expected, actual);
}
```

Observation: this test can be green while behavior is broken, and it fails on
any safe refactor that changes formatting or structure.

---

# Prompt

What is the closest real-world version of this anti-pattern in your tests?

<!--
Facilitation note: A SME story about removing a similar test can reinforce
improved change confidence.
-->

---

# What can be done (outcomes)

- Add at least one outcome assertion to interaction-only tests
- Check a user-visible result (return value, state change, side effect)
- Prefer fakes with state over mocks when outcomes are the goal

```csharp
var result = service.Place(order);
Assert.Equal(OrderStatus.Confirmed, result.Status);
```

---

# What can be done (replace bad tests)

- Delete tests that only assert implementation text or exact interaction scripts
- Replace them with behavior checks (inputs, outputs, user-visible effects)
- Use refactor breakage as a signal to ask what behavior is protected

```csharp
var total = service.Total("CA", 100m);
Assert.Equal(108m, total);
```

---

# What can be done (test levels)

- Promote critical flows to integration or functional tests
- Keep mocks minimal; one assertion per test that proves behavior
- Map gaps on the pyramid or diamond and pick one to address

```csharp
response.EnsureSuccessStatusCode();
Assert.Equal(108m, await response.Content.ReadFromJsonAsync<decimal>());
```

---

# Facilitation playbook: setup

- Shared whiteboard link (Miro, Hackerdraw, or similar)
- Three columns: Observation, Example, Question
- Link shared early; attendees keep it open

---

# Facilitation playbook: 2-minute prompts

1. Prompt read aloud and pasted on the whiteboard
2. Two-minute timer started
3. Everyone adds 1-2 sticky notes
4. Notes grouped quickly by theme; pick 2 to discuss

---

# Facilitation playbook: SME share-out

- After the initial share, a SME adds context or counterexample
- One practical heuristic: "What would you do differently next time?"

---

# Facilitation playbook: suggested prompts

- Where have you seen tests that pass but still allow a regression?
- What does a good test double strategy look like in your team?
- What is one test you would delete tomorrow, and why?

---

# Facilitation playbook: if time is tight

- One prompt per topic
- Clustering skipped; go straight to 2-3 quick shares

---

# One-sentence takeaway

Green tests are only useful when they protect behavior that matters to users.

---

# Next steps

- Use this deck end-to-end or jump to the sections you need
- Pick one test suite to audit for mock theatre
- Replace one brittle interaction test with an outcome test

---

# References

## Foundational texts

1. **Kent Beck** - *Test Driven Development: By Example* (2002)  
   The original TDD book, introduces Chicago/classical style

2. **Steve Freeman & Nat Pryce** - *Growing Object-Oriented Software, Guided by Tests* (2009)  
   The definitive London-school TDD guide

3. **Martin Fowler** - *Mocks Aren't Stubs* (2007)  
   [martinfowler.com/articles/mocksArentStubs.html](https://martinfowler.com/articles/mocksArentStubs.html)  
   Essential article distinguishing state vs behavior verification

---

# References (1 of 2)

## Critical perspectives

4. **Ian Cooper** - *TDD, Where Did It All Go Wrong?* (2013)  
   [youtube.com/watch?v=EZ05e7EMOLM](https://www.youtube.com/watch?v=EZ05e7EMOLM)  
   How TDD practice diverged from Kent Beck's original intent

5. **J.B. Rainsberger** - *Integrated Tests Are a Scam* (2009)  
   [blog.thecodewhisperer.com/permalink/integrated-tests-are-a-scam](https://blog.thecodewhisperer.com/permalink/integrated-tests-are-a-scam)  
   Critique of over-reliance on integrated tests

6. **Google Testing Blog** - *Increase Test Fidelity By Avoiding Mocks* (2024)  
   [testing.googleblog.com/2024/02/increase-test-fidelity-by-avoiding-mocks.html](http://testing.googleblog.com/2024/02/increase-test-fidelity-by-avoiding-mocks.html)  
   Recent industry perspective on mock overuse

---

# References (2 of 2)

## Practical guides

7. **ThoughtWorks** - *Mockists Are Dead. Long Live Classicists* (2017)  
   [thoughtworks.com/insights/blog/mockists-are-dead-long-live-classicists](https://thoughtworks.com/en-us/insights/blog/mockists-are-dead-long-live-classicists)  
   Industry shift toward classical testing

8. **Adrian Booth** - *Test Driven Development Wars: Detroit vs London* (2020)  
   [medium.com/@adrianbooth/test-driven-development-wars](https://medium.com/@adrianbooth/test-driven-development-wars-detroit-vs-london-classicist-vs-mockist-9956c78ae95f)  
   Balanced comparison of both schools

9. **Amazing CTO** - *Mocking is an Anti-Pattern* (2024)  
   [amazingcto.com/mocking-is-an-antipattern](https://www.amazingcto.com/mocking-is-an-antipattern-how-to-test-without-mocking/)  
   Practical alternatives to heavy mocking

10. **Instil** - *Mocking, Missteps and Maintenance Nightmares* (2024)  
    [instil.co/blog/mocking-missteps-and-maintenance-nightmares](https://instil.co/blog/mocking-missteps-and-maintenance-nightmares)  
    Real-world lessons on mock pitfalls
