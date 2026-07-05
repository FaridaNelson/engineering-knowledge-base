# Question Mark Scan Pattern

## Pattern

Scan a string from left to right while maintaining state about previously seen elements.

This pattern is useful when:

- the current character depends on previous characters,
- you need to count elements between two positions,
- you need to validate conditions while scanning,
- you want an **O(n)** solution without nested loops.

---

## Example Problem

Given a string containing letters, digits, and question marks:

- Find pairs of numbers that add up to 10.
- Verify that exactly three question marks occur between them.
- Return `false` if any valid pair violates this rule.

### Example

```text
arrb6???4xxbl5???eee5
```

### Output

```text
true
```

---

## Algorithm

1. Initialize state variables.
2. Scan the string from left to right.
3. Count question marks.
4. When a digit is encountered:
   - compare it with the previous digit,
   - check whether they add up to the target value,
   - validate the question mark count.
5. Update state and continue.

---

## Template

```javascript
let previous = null;
let counter = 0;
let found = false;

for (const char of str) {
  if (conditionA(char)) {
    counter++;
  }

  if (conditionB(char)) {
    const current = transform(char);

    if (previous !== null && validation(previous, current)) {
      found = true;

      if (counter !== expected) {
        return false;
      }
    }

    previous = current;
    counter = resetValue;
  }
}

return found;
```

---

## Solution

```javascript
function QuestionsMarks(str) {
  let previousNumber = null;
  let questionMarks = 0;
  let found = false;

  for (let i = 0; i < str.length; i++) {
    const char = str[i];

    if (char === "?") {
      questionMarks++;
    }

    if (/[0-9]/.test(char)) {
      const current = Number(char);

      if (previousNumber !== null && previousNumber + current === 10) {
        found = true;

        if (questionMarks !== 3) {
          return false;
        }
      }

      previousNumber = current;
      questionMarks = 0;
    }
  }

  return found;
}
```

---

## Complexity

- **Time:** O(n)
- **Space:** O(1)

---

## JavaScript Concepts Used

- String traversal
- State variables
- Regular expressions
- Number conversion
- Conditional validation

---

## Mistakes I Made

- Placed validation logic outside the loop.
- Used a variable (`char`) outside its scope.
- Confused assignment (`=`) with comparison (`===`).
- Forgot to reset the question mark counter after processing a number.

---

## Interview Takeaways

When you see problems involving:

- "between two elements",
- "count characters until",
- "remember previous value",
- "validate during traversal",

consider using a **stateful linear scan**.

### Examples

- Question mark counting
- Parentheses validation
- Bracket matching
- Run-length encoding
- Token parsing
- Log/event stream processing
- Sliding state machines
