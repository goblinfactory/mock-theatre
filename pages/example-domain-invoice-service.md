---
title: Example Domain Invoice Service
---

We will test a small orchestration flow:

- `InvoiceService` turns an `Order` into an `Invoice`
- Uses `ITaxCalculator` and `IDiscountPolicy`
- Persists via `IInvoiceRepository`
- Sends via `IEmailSender`

Goal: compare tests without changing the domain
