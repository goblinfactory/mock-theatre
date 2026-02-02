# 03. The useless test example

## Objective

Make the anti-pattern obvious in under 5 minutes.

## Key points

- This test verifies file text, not behavior.
- It fails on safe refactors and gives false confidence.

## Quick demo (the bad test)

```csharp
[Fact]
public void FooFile_contains_exactly_these_25_lines()
{
    var expected = new[] { "using System;", "...", "// line 25" };
    var actual = File.ReadAllLines("Foo.cs");
    Assert.Equal(expected, actual);
}
```

Observation: "Green tests like this do not protect user outcomes."

## 2-minute prompt

"What is the closest real-world version of this anti-pattern in your tests?"

## Facilitation note (invite SMEs)

A SME share on one test they would delete and why.

[Back to index](index.md)
