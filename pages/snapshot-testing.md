# Verify Example: Inline Snapshot

```csharp
[Fact]
public Task Payroll_calculation_produces_expected_itemization()
{
    var payroll = new PayrollService();
    var employee = new Employee 
    { 
        Id = 42, 
        Salary = 5000m, 
        TaxCode = "1257L" 
    };
    
    var payslip = payroll.Calculate(employee, new DateTime(2026, 1, 31));
    var lines = payslip.GetItemizedLines();
    
    // Verify inline - snapshot stored in code
    return Verify(lines);
}
```

**First run:** Creates `.verified.txt` file with the output  
**Subsequent runs:** Diffs against verified snapshot  
**On change:** Shows diff tool for approval or rejection

<small>Install: `dotnet add package Verify.Xunit`</small>
