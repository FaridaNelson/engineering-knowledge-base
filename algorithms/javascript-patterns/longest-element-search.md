# Find Maximum Element Pattern

Problem:
Find the longest word in a sentence.

Algorithm:

1. Clean the input.
2. Split into elements.
3. Keep track of the current maximum.
4. Return the maximum.

Example:

let best = "";

for (const item of items) {
if (item.length > best.length) {
best = item;
}
}

Complexity:
Time: O(n)
Space: O(1)

Lessons:

- Strings are immutable in JavaScript.
- .replace() returns a new string.
- Avoid sorting when only a maximum value is needed.
