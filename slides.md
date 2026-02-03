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

## and ...Chicago vs London testing 

- because we gotta talk about this!


---

## Apologies in advance ... if you were expecting a "speaker"

I'm just facilitating;

I was expecting this to be a physical meetup around table Lean Beers style, ala london .NET u/g style;

The Physical Setup: Multiple tables are spread across a pub. Each table has a stack of Post-its and Sharpies.

The Topic "Pitch": Instead of one speaker, attendees write topics on Post-its. On each table, these are grouped into "To Discuss," "Discussing," and "Done."

Dot Voting: People use their pens to put "dots" on the topics they care about most.

The "Roman Vote" (Thumb Voting): Every 5–8 minutes, the table moderator (or just the group) does a "thumbs up/down/sideways" to decide whether to keep talking about the current topic or move to the next.

The Rotation: Unlike a standard Lean Coffee, people are encouraged to "vote with their feet" and move between tables if the topic isn't grabbing them.

i.e. I'm more of facilitating, than presenting. if you're looking for pure speaker talks, there's some excellent talks <a href=''>referenced in the last slides. </a> today is going to be collaborative;

<small>I'll take notes, and save them to the repo;</small>




---

# Who am I?

<img src="/img/alan.png" class="h-40 mx-auto" />

- Alan Hemmings, contractor
- Working in Xero Payroll UK `Phoenix` project, part of the outputs team, based in Cambridge UK.
- My wife and I run Snowcode, a geek ski holiday-conference held at great ski resorts.
- Open source dev and occasional user group organiser and attendee.

---

# Why this (these 2) topics?

<v-clicks>

- I'm passionate about tesability love testing but I'm not a professional tester. (I like to pack my own parachute because I love sky diving, not because I want to be a professional chute packer.)
- I'm passionate about self diagnosing code.
- I recent saw some code that changed some behaviour, without a corresponding change to a test.
- There's some smart people that like mocks, so I want to learn more.
- What brings you guys to this session?
- ?
- ?

</v-clicks>

---

# What are some test anti-patterns?

discuss

-
-

---


# Why green tests can lie?

Discuss

- 
- 

---

# What is Chicago and London style testing?

(I had never heard this way of describing the testing before Friday, I had to look it up ) Volunteer please to explain ?

<v-clicks>

## Chicago Testing

-
-

## London Testing

-
-
</v-clicks>


---


# Chicago-style example

<v-clicks>


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

</v-clicks>

<v-clicks>

# London-style example

```csharp
var tax = new Mock<ITaxCalculator>();
tax.Setup(t => t.Calculate("CA", 100m)).Returns(8m);
var service = new OrderService(tax.Object);

var total = service.Total("CA", 100m);

Assert.Equal(108m, total);
tax.Verify(t => t.Calculate("CA", 100m), Times.Once);
```

</v-clicks>



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

# Q

Where have you seen green tests give false confidence?


---


# Mocks Arent Stubs

What's the difference between `Dummy`, `Fake`, `Stub`, `Spy`, `Mock` ?

discuss

<div class="grid grid-cols-2 gap-6">
<div>
<v-clicks>

- **Dummy**: passed around but never used

  ```csharp
  var svc = new BillingService(new DummyNotifier());
  ```

- **Fake**: working implementation with shortcuts (e.g., in-memory)

  ```csharp
  var repo = new InMemoryCustomerRepo();
  repo.Save(new Customer { Id = 1, Name = "Alice" });
  ```

- **Stub**: returns canned answers, no verification

  ```csharp
  var rates = new StubRateProvider(5.99m);
  Assert.Equal(14.98m, new ShippingService(rates).Calculate("UK", 2.5m));
  ```

</v-clicks>
</div>
<div>
<v-clicks>

- **Spy**: stub that records calls for later assertions

  ```csharp
  var email = new SpyEmailSender();
  new OrderService(email).PlaceOrder(123);
  Assert.Equal(1, email.SentCount);
  ```

- **Mock**: pre-programmed expectations; verifies interactions

  ```csharp
  var notifier = new Mock<INotifier>();
  new OrderService(notifier.Object).PlaceOrder(123);
  notifier.Verify(n => n.Send(123), Times.Once);
  ```

</v-clicks>
</div>
</div>

---

# Mock: verifies behavior

```csharp
[Fact]
public void Notifies_user_when_order_placed()
{
    var notifier = new Mock<INotifier>();
    var service = new OrderService(notifier.Object);

    service.PlaceOrder(orderId: 123);

    notifier.Verify(n => n.Send(123), Times.Once);
}
```

A **mock** checks that the right interaction happened.

---

# Stub: provides canned responses

```csharp
[Fact]
public void Calculates_shipping_cost()
{
    var rateProvider = new Mock<IRateProvider>();
    rateProvider.Setup(r => r.GetRate("UK")).Returns(5.99m);
    var service = new ShippingService(rateProvider.Object);

    var cost = service.Calculate("UK", weight: 2.5m);

    Assert.Equal(14.98m, cost);
}
```

A **stub** returns pre-set values (no verification).

---

# Fake: working lightweight implementation

```csharp
[Fact]
public void Saves_and_retrieves_customer()
{
    var repo = new InMemoryCustomerRepo(); // fake
    var service = new CustomerService(repo);

    service.Save(new Customer { Id = 1, Name = "Alice" });
    var result = service.Get(1);

    Assert.Equal("Alice", result.Name);
}
```

A **fake** has real logic but uses shortcuts (e.g. in-memory).

<v-clicks>

### Q : When is a fake better than a Stub?

Discuss

</v-clicks>


---

# What's the difference between a component test and an Integration Test?

Discuss

-
-

---

# Testing diamond (fat middle) instead of a testing Pyramid?

 Discuss

-
- 

--- 
 
# Test Pyramid

```
           /\
          /  \
         / UI \           ← Few, slow, brittle
        /______\
       /        \
      / Integr.  \        ← Some, medium speed
     /____________\
    /              \
   /  Unit Tests    \     ← Many, fast, focused
  /__________________\

```

--- 
# Where are the executable specifications?

<v-clicks>

- If we use a test pyramid then and you say, well, they are at the top, then that only gives you happy path testing.
- It doesnt make grey areas unambiguous with worked examples
- UX tests get created (most often) far too long after the work is done, and often by an entirely different team.  

</v-clicks>


---

# Test Diamond

```
           /\
          /  \
         / UI \           ← Few, slow
        /______\
       /        \
      /          \
     /  Service   \       ← Many, BDD/API tests
    /    (Fat      \         (Reqnroll, SpecFlow)
   /     Middle)     \
  /__________________ \
  \                  /
   \   Unit Tests   /     ← Some, fast
    \______________/

```

---
# BDD Test libraries

- Reqnroll
- Specflow et



# When tests lie

- Interaction-only tests can pass while outcomes are broken.
- Over-mocking hides integration mistakes and config errors.
- "Calls happened" assertions are easy to satisfy accidentally.
- Feet in concrete; slow you down when refactoring.

---

# Who should create mocks of services?

If you're dependant on an API from another team in your org, who you can talk to, who should create the service mock? (and why)


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

# Where is your team's biggest testing gaps?

(spend the bulk of the time here)

discuss, let subject matter experts share.

-
-

---

# Snapshot testing

---


# Approval tests

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

# References 


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

# References

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
