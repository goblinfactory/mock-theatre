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

# Two Styles Two Questions

**Chicago (state-based):**
- "After I run this, what state or result exists?"
- Verify outputs, return values, and persisted state

**London (interaction-based):**
- "Did I collaborate with dependencies the right way?"
- Verify calls, messages, and protocol with collaborators

<small>Source: https://martinfowler.com/articles/mocksArentStubs.html</small>

---

<div class="grid grid-cols-2 gap-6">
<div>

# Chicago-style example

<v-clicks>

```csharp
[Fact]
public void Late_payment_adds_fee_and_marks_delinquent()
{
    var clock = new FakeClock(new DateTime(2026, 2, 2));
    var repo = new InMemoryInvoiceRepo()
        .Add(new Invoice(id: 42, dueOn: new DateTime(2026, 2, 1), amount: 100m));
    var service = new BillingService(repo, clock, lateFeeRate: 0.10m);

    service.CloseInvoice(id: 42, amount: 100m);

    var invoice = repo.Get(42);
    Assert.Equal(InvoiceStatus.DelinquentPaid, invoice.Status);
    Assert.Equal(110m, invoice.TotalPaid);
}
```

</v-clicks>

</div>
<div>


# London-style example

<v-clicks>

```csharp
[Fact]
public void High_value_order_requires_3ds_and_idempotency()
{
    var gateway = new Mock<IPaymentGateway>();
    var service = new CheckoutService(gateway.Object);

    service.Pay(orderId: 123, amount: 600m);

    gateway.Verify(g => g.Charge(It.Is<ChargeRequest>(r =>
        r.Amount == 600m && r.Requires3ds && r.IdempotencyKey == "order-123"
    )), Times.Once);
}
```

</v-clicks>

</div>
</div>
