# 01. The one-minute anti-pattern

## Objective

In one minute, show a green test that proves nothing.

## Quick demo

```csharp
[Fact]
public void FooFile_contains_exactly_these_25_lines()
{
    var expected = new[] { "using System;", "...", "// line 25" };
    var actual = File.ReadAllLines("Foo.cs");
    Assert.Equal(expected, actual);
}
```

## The takeaway

Green tests that assert implementation text do not protect behavior. Prefer tests
that reflect user-visible outcomes.

[Back to index](index.md)
