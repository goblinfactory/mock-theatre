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
