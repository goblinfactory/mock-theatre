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
