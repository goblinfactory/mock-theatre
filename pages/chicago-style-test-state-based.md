---
title: Chicago Style Test State Based
---

```csharp
public sealed class InvoiceServiceTests
{
    [Fact]
    public void Creates_invoice_with_expected_totals()
    {
        var repo = new InMemoryInvoiceRepo();
        var service = new InvoiceService(
            new FixedTaxCalculator(0.10m),
            new FixedDiscountPolicy(5m),
            repo,
            new NullEmailSender());

        var order = Order.ForTotals(100m, "dev@example.com");

        var invoice = service.CreateAndSend(order);

        Assert.Equal(100m, invoice.Subtotal);
        Assert.Equal(5m, invoice.Discount);
        Assert.Equal(9.5m, invoice.Tax);
        Assert.Same(invoice, repo.Saved.Single());
    }
}
```

State-based verification: the invoice and repo state tell the story
