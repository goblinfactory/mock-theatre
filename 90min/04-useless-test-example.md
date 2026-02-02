# 04. The useless test example

## Objective

Make the anti-pattern obvious: a test that only checks code text, not behavior.

## Key points

- This is the extreme end of mock theatre: asserting implementation text.
- It tells you nothing about behavior or correctness.
- It actively discourages refactoring, even safe refactoring.

## Quick demo (the bad test)

```csharp
[Fact]
public void FooFile_contains_exactly_these_25_lines()
{
    var expected = new[]
    {
        "using System;",
        "",
        "namespace Demo",
        "{",
        "    public class Foo",
        "    {",
        "        public int Add(int a, int b)",
        "        {",
        "            return a + b;",
        "        }",
        "",
        "        public int Multiply(int a, int b)",
        "        {",
        "            return a * b;",
        "        }",
        "    }",
        "}",
        "",
        "// line 19",
        "// line 20",
        "// line 21",
        "// line 22",
        "// line 23",
        "// line 24",
        "// line 25"
    };

    var actual = File.ReadAllLines("Foo.cs");

    Assert.Equal(expected, actual);
}
```

Talk track: "This test can be green while behavior is broken, and it fails on any
safe refactor that changes formatting or structure."

## 2-minute prompt

"What is the closest real-world version of this anti-pattern in your tests?"

## Facilitation note (invite SMEs)

Ask a SME to share a story where removing a similar test improved change
confidence.

[Back to index](index.md)
