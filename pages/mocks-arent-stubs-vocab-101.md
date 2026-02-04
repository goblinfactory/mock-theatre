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
