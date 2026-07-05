# Question Mark Scan Pattern

## Pattern

Scan a string from left to right while maintaining state about previously seen elements.

This pattern is useful when:

- the current character depends on previous characters,
- you need to count elements between two positions,
- you need to validate conditions while scanning,
- you want an O(n) solution without nested loops.

---

## Example Problem

Given a string containing letters, digits, and question marks:

- Find pairs of numbers that add up to 10.
- Verify that exactly three question marks occur between them.
- Return false if any valid pair violates this rule.

Example:

```text
arrb6???4xxbl5???eee5
```

Output:

```text
true
```
