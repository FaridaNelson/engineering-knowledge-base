# Set Lookup Pattern

## Example Problem

Find Intersection

## Pattern

1. Convert one collection into a Set.
2. Iterate through the second collection.
3. Use Set.has() for O(1) lookups.
4. Collect matching results.

## Example

```js
const set = new Set(arr1);

for (const item of arr2) {
  if (set.has(item)) {
    result.push(item);
  }
}
```

## Contains Duplicate (Python)

seen = set()

for item in nums:
if item in seen:
return True

    seen.add(item)

return False

## Common Use Cases

- Duplicate detection
- Array intersection
- Membership checking
- Fast lookup
- Frequency counting (with HashMap/Dictionary)
