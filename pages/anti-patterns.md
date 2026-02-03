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

# When tests lie

<v-clicks>

- Interaction-only tests can pass while outcomes are broken.
- Over-mocking hides integration mistakes and config errors.
- "Calls happened" assertions are easy to satisfy accidentally.
- Feet in concrete; slow you down when refactoring.
</v-clicks>

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
