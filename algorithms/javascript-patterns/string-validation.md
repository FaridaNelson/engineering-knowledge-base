# String Validation Pattern

Problem:
Validate a username according to a set of rules.

Concepts learned:

- Input validation
- String length checks
- Regular expressions
- Character classes
- Start/end anchors

Useful regex:

^[A-Za-z][A-Za-z0-9_]\*$

Meaning:
^ start of string
[A-Za-z] first character must be a letter
[A-Za-z0-9_]\* zero or more valid characters
$ end of string

Additional validation:
str[str.length - 1] !== "\_"

Complexity:
O(n)
