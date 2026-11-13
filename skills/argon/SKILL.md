---
name: argon
description: Write, read, debug and run code in the Argon programming language (.ar files) using the Chloride interpreter (`argon`) and the Isotope package manager (`isotope`). Use whenever a task involves .ar files, Argon syntax, the Argon standard library, argon-package.json / iso-lock.json, Isotope packages, or Chloride native extensions.
---

# Writing Argon

Argon is a dynamically typed scripting language. Chloride (`argon`) is its interpreter and
Isotope (`isotope`) is its package manager. It *looks* like Python/JavaScript, but many
details differ. **Don't assume Python or JS semantics.** Check the rules below, and always
run the code.

Describes Chloride v4.1.0 (check with `argon --version`; see "If you're on v4.0.3 or
older" below) and Isotope 1.0.0. Full docs: the `README.md` tree in the
Argon docs repository. For the stdlib and Isotope, read [reference/stdlib.md](reference/stdlib.md)
and [reference/isotope.md](reference/isotope.md).

## Workflow

1. Write the code to a `.ar` file.
2. Run it with `argon file.ar` (the `.ar` is optional). Uncaught errors print
   `ERROR! <Type>: <message>` with the file:line:column to stderr and exit with status 1.
3. Fix and rerun. For quick experiments, pipe code in: `echo 'term.log(1 + 1)' | argon`.
4. Never name a file after a stdlib module you import (`json.ar`, `build.ar`, `path.ar`...).
   Local files shadow the stdlib, and a file importing its own name throws `ImportError`.

## Core syntax

```argon
# comments start with #
let name = "world"                       # declare with let; redeclaring in the same scope is an error
let a = 1, b = 2                         # several declarations
term.log(`Hello, $(name)!`)             # print; template strings interpolate $( expr )

let add(a, b) = a + b                    # single-expression function
let greet(who, greeting = "Hi") = do     # block body: `do` at END of line, body indented
    let message = `$(greeting), $(who)`
    return message                       # no return => null

let square = (x) = x * x                 # anonymous function
term.log(add(1, 2), greet("Ada"), greet("Ada", greeting = "Yo"), square(4))

if (a > b) do                            # condition always in parentheses
    term.log("a is bigger")
else if (a == b) term.log("equal")       # single-statement bodies need no `do`
else term.log("b is bigger")

for (i in 0 until 3) term.log(i)         # 0 1 2 (until = exclusive)
for (i in 1 to 3) term.log(i)            # 1 2 3 (to = inclusive)
for ([key, value] in {x: 1, y: 2}) term.log(key, value)

let n = 0
while (n < 3) n += 1
```

### Blocks and indentation

- `do` must be the **last token on its line**; the indented lines below are the body. The
  block ends when indentation returns to the outer level. Indentation is significant, and
  every line in a block needs the same indentation (use 4 spaces).
- A `do` block is an expression whose value is what it `return`s:
  `let x = do` + an indented body with `return ...`.
- When an anonymous function with a `do` body is a call argument, close the call on a new
  line at the call's indentation:

```argon
let run(f) = f()
let result = run(() = do
    let total = 0
    for (i in 1 to 4) total += i
    return total
)
term.log(result)   # 10
```

- Multi-line expressions are fine after an operator, a comma, inside brackets, or with
  leading `.method()` chains.
- `if`, `while`, `for` and `try` are statements, not expressions (`let y = if (...) 1` is
  a syntax error). Use `cond ? a : b`.
- `throw` is a statement: `let f() = throw X()` is invalid. Use a `do` block.

## Python/JS habits that are WRONG in Argon

| Don't write | Write instead | Why |
|---|---|---|
| `2 ** 3` | `2 ^ 3` | `^` is exponent |
| `x ** y ** z` | `x ^ y ^ z` | `^` is right-associative and binds tighter than unary minus (`-2 ^ 2 == -4`) |
| `if (items)` to test non-empty | `if (items.length)` | `[]` and `{}` are **truthy**; only `false`, `null`, `0`, `""` are falsy |
| `"n=" + 5` | `` `n=$(5)` `` or `"n=" + string(5)` | `+` doesn't convert types |
| `s.strip()` | `s.strip(" \t\n\r")` | `strip` requires the set of chars |
| `arr.sort()` | `arr.sort((a, b) = a - b)` | comparator is required; it sorts in place and returns the array |
| `", ".join(arr)` | `arr.join(", ")` | array method; strings only |
| `len(x)` | `x.length` | property; dictionaries have none, so use `array(d).length` |
| `type(x) == "number"` | `type(x) == number` or `type(x).__name__ == "number"` | `type()` returns a class |
| `isinstance` | `is_instance(x, number)` | |
| `d["missing"]` expecting `null` | `d.value_get("missing", default)` or `"k" in d` | missing keys throw `AttributeError` |
| `[1] == [1]` expecting `true` | compare element-wise | `==` on arrays and dictionaries is identity |
| `*args` used as an array | `array(args)` | `*args` is a **tuple** (immutable) |
| `def f(x=[])` style defaults | `let f(x = null)` + create inside | defaults are evaluated **once**, at definition |
| `tuple([1, 2])` to convert | `tuple.of([1, 2])` | `tuple(a, b)` takes the items as arguments |
| `print(...)` / `console.log` | `term.log(a, b, sep=", ", end="\n")` | |
| `term.error("text")` | `term.error(exception_object)` | to write raw text to stderr use `file.stderr` (binary, takes a buffer) |
| `try ... finally` | catch, clean up, rethrow | no `finally` |
| `catch (e)` | `catch (Exception as e)`, `catch (Exception)` or bare `catch` | bare `catch` doesn't catch `StopIteration`/`KeyboardInterrupt` |
| `while True:` | `forever do` or `while (true) do` | |
| float rounding like `0.1 + 0.2 != 0.3` | | numbers are exact rationals: `0.1 + 0.2 == 0.3` |

More facts that differ:

- Logic is `!x`, `x && y`, `x || y`, never `not`/`and`/`or` (`not` only appears in
  `not in`). `&&` and `||` short-circuit and return an operand, so `name || "default"` works.

- Strings are UTF-8 **bytes**: `.length`, indexing, slicing and iteration are by byte
  (`"é".length == 2`).
- Precedence (high to low): call/index/attribute, `^`, unary `-` `!`, `* / // %`, `+ -`,
  ranges `to`/`until`, `< > <= >= in not in`, `== !=`, `&&`, `||`, `? :`. So
  `0 until n + 1` and `-1 to 3` work without parentheses.
- `//` floors and `%` takes the divisor's sign, like Python (`(-7) // 2 == -4`).
- Dictionary keys: a bare identifier key is a string (`{name: 1}` means `{"name": 1}`); use
  `{(expr): v}` for computed keys; `{name}` is shorthand for `{name: name}`. Read with
  `d.key` or `d["key"]`.
- Assigning to an undeclared name creates it in the current scope. Prefer `let`.
- Each `do` block is a new scope. Closures can read and update outer variables.

## Values and built-ins

- Types (all callable as converters): `number`, `string`, `boolean`, `array`, `tuple`,
  `dictionary`, `buffer`, `function`, `range`, `slice`, `object`, `type`.
- Literals: `null`, `true`, `false`, `0xff`, `0o17`, `0b101`, `1e6`, `.5`; strings in
  `"..."` or `'...'` (single line); template strings in backticks (multi-line, `$(expr)`).
  Escapes: `\n \t \r \b \f \\ \/ \" \' \` \$ \uXXXX` (use `\$(` for a literal `$(` in a
  template); any other escape is a `SyntaxError`.
- Array: `.length`, `.append(*v)`, `.insert(i, *v)`, `.pop(i?)`, `.map(f)`, `.filter(f)`,
  `.sort(cmp)`, `.join(sep)`, `array.of_size(n)`, `delete a[i]`, slicing `a[start:stop:step]`,
  negative indexes, `in` / `not in`. Copy with `array(a)`.
- String: `.upper()`, `.lower()`, `.title()`, `.replace(old, new)`, `.split(sep, max?)`,
  `.strip(chars)`, `.repeat(n)`, `.starts_with(s)`, `.ends_with(s)`, `.index_of(s, from?)`,
  `.last_index_of(s, from?)`, `.ord()`, `.to_byte()`, `string.chr(cp)`, `string.from_byte(b)`.
- Dictionary: `d.value_get(k, default?)`, `delete d.k`, `for ([k, v] in d)`,
  `dictionary(pairs)`. Insertion ordered.
- Tuple: `tuple(a, b)`, `tuple.of(iterable)`, indexing, slicing, `.length`.
- Buffer (bytes): `buffer.from_string(s)`, `buffer.of_size(n)`, `.to_string()`,
  `.extend(b)`, `.split(sep_buf)`, `.partition(sep_buf)`, `b[i]` gives a number 0-255.
- Ranges: `a to b` (inclusive), `a until b` (exclusive), `range(stop)`,
  `range(start, stop, step?)`. All lazy. `x in 1 to 10` is true only for values the range
  would produce (`2.5 in 0 to 5` is false); use `a <= x && x <= b` for interval checks.
- Globals: `term` (`log`, `error`, `input(prompt?)`), `program` (`main`, `file.path`,
  `file.name`, `file.directory`, `cwd`, `exc`, `origin`), `platform` (`os`, `arch`,
  `lib_ext`, `lib_prefix`, `args`), `env` (dictionary), `is_instance`, `load_native_code`.
- Command line arguments: `platform.args[2:]` (index 0 is the interpreter and 1 is the
  script).

## Destructuring

```argon
let [first, *rest] = [1, 2, 3]
let [x, [y, z]] = [1, [2, 3]]
let {name, version: v} = {name: "argon", version: 4}
term.log(first, rest, x, y, z, name, v)    # 1 [2, 3] 1 2 3 argon 4
for ({name} in [{name: "a"}, {name: "b"}]) term.log(name)
```

## Functions

```argon
let f(x, label?, scale = 1, *args, **kwargs) = do   # label? defaults to null
    return [x * scale, label, args, kwargs]

term.log(f(2))                         # [2, null, tuple(), {}]
term.log(f(2, "l", 3, 4, 5, k = 6))    # [6, "l", tuple(4, 5), {"k": 6}]
term.log(f(*[1, "a"], **{scale: 10}))  # spread at call site
```

Order: required, then defaults/optional, then `*args`, then `**kwargs`.

## Classes

```argon
class Shape do
    this.count = 0                                  # class attribute
    let secret = "private"                          # private to the class body
    this.__init__(self, name) = do
        self.name = name                            # instance attribute
        Shape.count += 1
    this.area(self) = 0
    this.__string__(self) = `$(self.name) with area $(self.area())`
    this.describe() = `$(this.count) shapes`        # no self => static, call on the class

class Square(Shape) do                              # inheritance
    this.__init__(self, side) = do
        Shape.__init__(self, "square")              # call the parent explicitly
        self.side = side
    this.area(self) = self.side ^ 2
    this.get_perimeter(self) = self.side * 4        # property: sq.perimeter

let sq = Square(3)
term.log(sq, sq.perimeter, Shape.describe(), is_instance(sq, Shape))
```

- `this` inside the class body is the class; `self` is the instance (first parameter).
- `get_x(self)` / `set_x(self, v)` make a property `x`.
- Empty classes: `class MyError(Exception) null` or `class Empty do`.
- Operator methods: `__add__ __subtract__ __multiply__ __division__ __floor_division__
  __modulo__ __exponent__ __negation__ __equal__ __not_equal__ __less_than__
  __less_than_equal__ __greater_than__ __greater_than_equal__ __contains__ __getitem__
  __setitem__ __delitem__ __getattr__ __setattr__ __call__ __iter__ __next__ __hash__
  __string__ __repr__ __number__ __boolean__ __template__`.
- Iterators: `__iter__` returns an object whose `__next__` returns values and does
  `throw StopIteration()` when done.
- Class attributes are inherited (`Square.count` reads `Shape.count`), but assigning
  `Square.count = 1` creates a separate attribute on `Square`; update `Shape.count` to share.

## Errors

```argon
class AppError(Exception) null

let risky(n) = do
    if (n < 0) throw AppError(`negative: $(n)`)
    return n

try do
    risky(-1)
catch (AppError as e) do
    term.log(type(e).__name__, e.message)    # AppError negative: -1
```

Built-ins: `Exception` > `RuntimeError, ValueError, TypeError, IndexError, NameError >
AttributeError, MathsError > ZeroDivisionError, FileError > PathError, ImportError, ...`. Module
exceptions live on the module (`file.FileNotFoundError`, `regex.RegexError`,
`network.SocketError`). `e.stack_trace` is an array of `tuple(file, line, col, len)`.

## Modules

```
import "json" as json                     # module as a dictionary
import "file" expose open, is_file        # pull names into scope
import "./helpers" expose helper as h     # rename; .ar and /init.ar are implied
if (program.main) main()                  # only when run directly
```

Resolution order: relative to the importing file, then `argon_modules/` in that directory
and each parent directory, then the installation's stdlib and global `argon_modules`. Every
top-level `let` is exported. Modules run once and are cached. Compiled bytecode goes in
`__arcache__/` (gitignore it).

## Known issues

- Isotope 1.0.0: `isotope remove -g` / `update -g` treat `-g` as a package name, and
  `package push --remote` is ignored.

## If you're on v4.0.3 or older

If `argon --version` reports a version before `v4.1.0`, these differ:
- Every operator had its own precedence level (`8 / 2 * 2 == 2`, `2^3^2 == 64`,
  `-2^2 == 4`), and ranges bound tighter than everything. **Parenthesise all mixed
  expressions.**
- `//` and `%` truncated toward zero for integers.
- `exponent()` and custom `^` called `__multiply__`.
- There was no `` \` ``, `\$` or `forever`, and ranges didn't support `in`.
- `Child.attr` didn't find inherited class attributes.
- `random.int(a, b)` returned `[a, a + b)`.
- `File.read()` reread from the start.
- `file.stdnull` didn't work with `subprocess`.
- `json.stringify` rejected tuples.
- `file.FileError` was separate from the global one.
- `date` had no `Duration`, arithmetic, ISO parsing or `utc=` options, and `from_string`
  parsed local time.
