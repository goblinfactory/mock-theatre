---
title: London Style Test Interaction Based
---

```csharp
public sealed class InvoiceServiceTests
{
    [Fact]
    public void Persists_and_sends_invoice()
    {
        var repo = new Mock<IInvoiceRepository>();
        var email = new Mock<IEmailSender>();
        var service = new InvoiceService(
            new FixedTaxCalculator(0.10m),
            new FixedDiscountPolicy(5m),
            repo.Object,
            email.Object);

        var order = Order.ForTotals(100m, "dev@example.com");

        var invoice = service.CreateAndSend(order);

        repo.Verify(r => r.Save(It.Is<Invoice>(i => i.Id == order.Id)), Times.Once);
        email.Verify(e => e.SendInvoice(order.CustomerEmail, invoice), Times.Once);
    }
}
```

Interaction-based verification: correct collaborations are the signal
