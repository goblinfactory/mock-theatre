# Failure mode example

```csharp
var inventory = new Mock<IInventory>();
var service = new OrderPlacer(inventory.Object);

service.Place(order);

inventory.Verify(i => i.Reserve(order.Id), Times.Once);
```

Note: this passes even if `Place` returns the wrong result, the confirmation
email is never sent, or the wrong order ID is passed in.
