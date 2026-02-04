# Approval testing (C# example)

```csharp
[Fact]
public void Invoice_pdf_matches_approved_output()
{
    var pdf = new InvoiceRenderer().Render(orderId: 42);
    Approvals.VerifyPdf(pdf);
}
```

Be careful with approval tests, as it will launch  a  diff viewer, beyond compare etc, if there's a regression or change that requires human approval. THis is by design, but will FREAK out developers not used to it.

#elementOfLeastSuprise

Best for solo projects imo. If you can use it, it's just freaking awesome!    
