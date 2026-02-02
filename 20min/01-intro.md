# 01. Intro and framing

## Objective

Define "mock theatre" and set expectations for the short format.

## Key points

- Mock theatre is tests that verify the script, not the outcome.
- Green tests can hide risk if they only confirm implementation details.
- We'll focus on test types and one anti-pattern to watch for.

## Quick demo (30-45 seconds)

```csharp
[Fact]
public void Sends_welcome_email()
{
    var sender = new Mock<IEmailSender>();
    var service = new OnboardingService(sender.Object);

    service.Welcome("alice@example.com");

    sender.Verify(s => s.Send(It.IsAny<string>(),
        It.IsAny<string>(), It.IsAny<string>()), Times.Once);
}
```

Note that this says nothing about the email content or user impact.

## 2-minute prompt (optional)

"Where have you seen green tests give false confidence?"

## Facilitation note (invite SMEs)

If time allows, one quick SME example at the end.

[Back to index](index.md)
