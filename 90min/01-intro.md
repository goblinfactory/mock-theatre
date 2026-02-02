# 01. Intro and framing

## Objective

Align on what "mock theatre" means and why green tests can still hide risk.

## Key points

- Mock theatre is when tests verify a script of interactions rather than useful outcomes.
- Green tests can be misleading if they only confirm implementation details.
- The cost is real: slow change, brittle tests, and false confidence.
- We will explore when mocks help and when they hurt.

## Quick demo (30-60 seconds)

Show a test that is green but over-specified:

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

Call out that changing the wording of the email breaks the test even if the user
still gets the email and the business rule is satisfied.

## 2-minute prompt

"Where have you seen green tests give false confidence?"

## Facilitation note (invite SMEs)

Ask anyone who has maintained a fragile test suite to share one example and how
they recognized it.

[Back to index](index.md)
