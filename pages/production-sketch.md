---
title: Production Sketch
---

```csharp
public sealed class InvoiceService
{
    private readonly ITaxCalculator _tax;
    private readonly IDiscountPolicy _discounts;
    private readonly IInvoiceRepository _repo;
    private readonly IEmailSender _email;

    public InvoiceService(
        ITaxCalculator tax,
        IDiscountPolicy discounts,
        IInvoiceRepository repo,
        IEmailSender email)
    {
        _tax = tax;
        _discounts = discounts;
        _repo = repo;
        _email = email;
    }

    public Invoice CreateAndSend(Order order)
    {
        var subtotal = order.Subtotal();
        var discount = _discounts.Calculate(order);
        var tax = _tax.Calculate(subtotal - discount, order.ShippingAddress);
        var invoice = new Invoice(order.Id, subtotal, discount, tax);

        _repo.Save(invoice);
        _email.SendInvoice(order.CustomerEmail, invoice);
        return invoice;
    }
}
```
