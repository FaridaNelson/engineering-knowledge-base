# JavaScript Regex Cheat Sheet

^ beginning of string
$ end of string
[] character class
[^] negated character class

-      zero or more

*      one or more
  ? optional
  \d digit
  \w word character
  \s whitespace

Examples:

/^[A-Za-z][A-Za-z0-9_]\*$/
/[^A-Za-z0-9 ]/g
