# `regex`

```argon
import "regex" as regex
```

Regular expressions, using the [PCRE2](https://www.pcre.org/current/doc/html/pcre2syntax.html)
syntax (the same as Perl and most other languages).

Remember that `\` must be escaped inside Argon strings, so the pattern `\d+` is written
`"\\d+"`.

## `regex.Regex(pattern)`

Compiles a pattern. An invalid pattern throws `regex.RegexError`:

```argon
import "regex" as regex

let number = regex.Regex("[0-9]+")

try regex.Regex("[")
catch (regex.RegexError as e) term.log("invalid pattern")
```

## Methods

| Method | Returns |
|---|---|
| `.match(subject)` | `true` if the pattern matches **anywhere** in `subject` |
| `.find(subject)` | the first match as an array `[whole_match, group1, group2, ...]`, or `[]` if there is no match |
| `.find_all(subject)` | an array of every match, each in the same form as `find` |
| `.group(subject, index)` | capture group `index` of the first match (`0` is the whole match) |
| `.replace(subject, replacement)` | `subject` with the first match replaced |
| `.replace_all(subject, replacement)` | `subject` with every match replaced |
| `.split(subject)` | `subject` split on every match |
| `.close()` | free the compiled pattern |

Anchor the pattern with `^` and `$` to test whether a whole string matches.

```argon
import "regex" as regex

let re = regex.Regex("([a-z]+)([0-9]+)")
term.log(re.match("abc123"), re.match("!!"))      # true false
term.log(re.find("x abc123 def45"))               # ["abc123", "abc", "123"]
term.log(re.find_all("abc123 def45"))             # [["abc123", "abc", "123"], ["def45", "def", "45"]]
term.log(re.group("abc123", 2))                   # 123
term.log(re.replace_all("abc123 def45", "N"))     # N N

term.log(regex.Regex(",\\s*").split("a, b,c"))    # ["a", "b", "c"]
term.log(regex.Regex("^[0-9]+$").match("123x"))  # false
```

## `Regex.intern(pattern)`

Returns a shared, cached compiled regex for `pattern`, compiling it only the first time.
This is useful for patterns used repeatedly in a loop or function:

```argon
import "regex" as regex

let is_word(s) = regex.Regex.intern("^[a-z]+$").match(s)
term.log(is_word("hello"), is_word("hi there"))   # true false
```

Interned patterns can't be closed; calling `close()` on one throws a `RuntimeError`.

## Next

- [`network`](../network/README.md)
