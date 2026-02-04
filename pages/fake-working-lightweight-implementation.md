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
