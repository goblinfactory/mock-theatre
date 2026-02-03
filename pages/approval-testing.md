# Snapshot testing

---

# Approval tests

---

# Approval testing: what it is

- Capture a full output as a **golden master**
- Diff on change: you approve the new output or fix the code
- Great for complex outputs (JSON, reports, PDFs, UIs)
- Keep inputs stable so diffs are meaningful

---

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

---

# Keeping docs and code in sync

- **MarkdownSnippets** auto‑pulls code into Markdown docs
- Docs stay current because snippets are compiled and tested
- If the code changes, the docs update or the build fails
- Similar to Go's executable examples (docs are tests)

Link: https://github.com/SimonCropp/MarkdownSnippets
