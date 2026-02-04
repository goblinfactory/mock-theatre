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
