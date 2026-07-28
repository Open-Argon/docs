# Running the REPL

Before you can use Argon's REPL, start it by running:
```bash
argon
```

This launches the REPL, which looks something like this:
```
Chloride v4.0.2-2-g97ad5ce Copyright (C) 2026 William Bell
This program comes with ABSOLUTELY NO WARRANTY; for details type "license".
This is free software released under the GNU GPL Version 3 or later,
and you are welcome to redistribute it under certain conditions; type "license" for details.
You can learn more about what the term "free software" means at https://www.gnu.org/philosophy/free-sw.html

>>> 
```

You can enter code directly at the prompt. For example:
```
>>> let x = 1
1
>>> x+=1
2
>>> let f(x) = 2*x^2 + 3*x + 10
<function f at 0x7f6df3b9ee00>
>>> f(x)
24
>>> 
```

To write multi-line code, end a line with the `do` keyword. The REPL will continue on a new indented line. To finish the block, enter a blank line:
```
>>> let f(x) = do
...     x*=10
...     return x
...     
... 
<function f at 0x7f6df3b9e000>
>>> 
```