# Mocks Arent Stubs (vocab 101)

What's the difference between `Dummy`, `Fake`, `Stub`, `Spy`, `Mock` ?

discuss : it's ok for a team to prefer different definitions, as long as they make it clear in advance that's what they're doing.

<div class="grid grid-cols-2 gap-6">
<div>
<v-clicks>

- **Dummy**: passed around but never used

  ```csharp
  var svc = new BillingService(new DummyNotifier());
  ```

- **Fake**: working implementation with shortcuts (e.g., in-memory)

  ```csharp
  var repo = new InMemoryCustomerRepo();
  repo.Save(new Customer { Id = 1, Name = "Alice" });
  ```

- **Stub**: returns canned answers, no verification

  ```csharp
  var rates = new StubRateProvider(5.99m);
  Assert.Equal(14.98m, new ShippingService(rates).Calculate("UK", 2.5m));
  ```

</v-clicks>
</div>
<div>
<v-clicks>

- **Spy**: stub that records calls for later assertions

  ```csharp
  var email = new SpyEmailSender();
  new OrderService(email).PlaceOrder(123);
  Assert.Equal(1, email.SentCount);
  ```

- **Mock**: pre-programmed expectations; verifies interactions

  ```csharp
  var notifier = new Mock<INotifier>();
  new OrderService(notifier.Object).PlaceOrder(123);
  notifier.Verify(n => n.Send(123), Times.Once);
  ```

</v-clicks>
</div>
</div>

---

# Martin Fowler

<div class="text-2xl leading-relaxed">

> Of these kinds of doubles, only mocks insist upon behavior verification.  
> The other doubles can, and usually do, use state verification.  
> Mocks actually do behave like other doubles during the exercise phase, as they need to make the SUT believe it's talking with its real collaborators — but mocks differ in the setup and the verification phases.

</div>

<div class="text-sm opacity-70">

  <br/>
  <A href="https://martinfowler.com/articles/mocksArentStubs.html#TheDifferenceBetweenMocksAndStubs">https://martinfowler.com/articles/mocksArentStubs.html#TheDifferenceBetweenMocksAndStubs</a>


That being said; what you agree with your team is what counts. Plus, put it in writing. 


</div>


---

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

---

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

---

# Fake: working lightweight implementation

```csharp
[Fact]
public void Saves_and_retrieves_customer()
{
    var repo = new InMemoryCustomerRepo(); // fake
    var service = new CustomerService(repo);

    service.Save(new Customer { Id = 1, Name = "Alice" });
    var result = service.Get(1);

    Assert.Equal("Alice", result.Name);
}
```

A **fake** has real logic but uses shortcuts (e.g. in-memory).

<v-clicks>

### Q : When is a fake better than a Stub?

Discuss

</v-clicks>
