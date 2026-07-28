# `regex`

```
import "regex" as regex
```

Provides regular expression matching, searching, replacing, and splitting using compiled regular expressions.

## `regex.Regex(pattern)`

Creates a compiled regular expression from a pattern.

```
let re = regex.Regex("[0-9]+")

term.log(re.match("123"))
```

Compiled regular expressions can be reused multiple times.

## `Regex.intern(pattern)`

Creates or retrieves a shared compiled regular expression.

Interned regular expressions are cached and reused when the same pattern is requested multiple times.

```
let a = regex.Regex.intern("[a-z]+")
let b = regex.Regex.intern("[a-z]+")

term.log(a == b)
```

Interned regular expressions are managed automatically and cannot be manually freed.

## `Regex.close()`

Frees the resources used by a regular expression.

```
let re = regex.Regex("hello")

re.close()
```

Calling `close()` on an interned regular expression raises a `RuntimeError`.

## `Regex.match(subject)`

Checks whether the regular expression matches the given string.

```
let re = regex.Regex("^hello")

term.log(re.match("hello world"))
```

Returns the match result from the regular expression engine.

## `Regex.find(subject)`

Finds the first match in a string.

```
let re = regex.Regex("[0-9]+")

let result = re.find("abc123def")

term.log(result)
```

Returns the first matching result, or an empty result if no match is found.

## `Regex.find_all(subject)`

Finds all matches in a string.

```
let re = regex.Regex("[0-9]+")

let results = re.find_all("abc123def456")

term.log(results)
```

Returns an array containing every match.

## `Regex.replace(subject, replacement)`

Replaces the first match with the given replacement.

```
let re = regex.Regex("[0-9]+")

term.log(re.replace("abc123", "x"))
```

## `Regex.replace_all(subject, replacement)`

Replaces every match with the given replacement.

```
let re = regex.Regex("[0-9]+")

term.log(re.replace_all("abc123def456", "x"))
# "abcxdefx"
```

## `Regex.split(subject)`

Splits a string using the regular expression as the delimiter.

```
let re = regex.Regex(",")

let parts = re.split("one,two,three")

term.log(parts)
# ["one", "two", "three"]
```

## Errors

Invalid regular expression patterns raise `RegexError`.

```
regex.Regex("[")
# RegexError
```

## Example

```
let number = regex.Regex("[0-9]+")

let text = "There are 123 apples and 456 oranges"

term.log(number.find_all(text))
# ["123", "456"]

term.log(number.replace_all(text, "N"))
# "There are N apples and N oranges"
```

## Next

- [`json`](../json/README.md)
- [`network`](../network/README.md)