# Variable Assignment

To define a variable in Argon, you can use either the `let` keyword or simply assign a value to a variable name without using `let`. Here's how you can define a variable using both methods:

Method 1: Using the `let` keyword

To define a variable using the `let` keyword, you need to specify the variable name and its initial value. Here's an example:
```javascript
let x = 10
```

In this example, we define a variable named `x` and initialise it with a value of `10`. The `let` keyword tells the Argon interpreter to define the variable on the current stack. If `x` already exists in the current stack, then it will throw a `Name Error` of `Name Error: variable "x" already exists`.

Method 2: Assigning a value to a variable name

You can also define a variable by simply assigning a value to a variable name without using `let`. Here's an example:
```javascript
x = 10
```

In this example, we define a variable named `x` and initialise it with a value of `10`. If the variable does not exist on any of the stacks below the current, then it will be created on the current stack. If the variable already exists on any of the stacks below the current, then it will be replaced on the highest stack that contains the variable.

It's worth noting that if you define a variable without using let, it will be created on the current stack, and if you define a variable using let, it will also be created on the current stack but won't be replaced on a lower stack. This means that variables defined with let are only accessible within the current scope and cannot be accessed from nested scopes.

Here's an example that demonstrates this:
```javascript
let x = 10

do
  let x = 20
  term.log(x) # Output: 20

term.log(x) # Output: 10
```

In this example, we define a variable `x` with a value of `10` using `let`. We then create a new block using the `do` keyword, which creates a new scope. Within this scope, we define a new variable `x` with a value of `20` using let. When we log the value of `x` using `term.log` within the scope, we get the value `20`. However, when we log the value of `x` outside of the scope, we get the value `10`, which is the value of the `x` variable defined in the outer scope. This demonstrates how variables defined with `let` are only accessible within the current scope and cannot be accessed from nested scopes.

```javascript
let x = 10

do
  x = 20
  term.log(x) # Output: 20

term.log(x) # Output: 20
```

In this example, we define a variable `x` with a value of `10` using `let`. We then create a new block using the `do` keyword, which creates a new scope. Within this scope, we re-assign the value of `x` to `20` without using `let`. When we log the value of `x` using `term.log` within the scope, we get the value `20`. When we log the value of x outside of the scope, we also get the value `20`, because the re-assignment of `x` in the nested scope affects the value of `x` in the outer scope. This demonstrates how re-assigning a variable without using let will replace the value of the variable in the current scope, and will also affect the value of the variable in any enclosing scopes.

# Function Assignment

`let` works exactly the same way as it does in variable assignment.

```javascript
let square(x) = do
  return x * x
```
In this example, we define a function named `square` with a parameter `x`. The function body uses a `do` block to group multiple statements together, and then returns the result of `x * x`. Note that the `return` keyword is used to indicate that the value of the expression `x * x` is the result of the function. This syntax is useful when you need to group multiple statements together or do some additional processing before returning a result.

```javascript
let square(x) = x * x
```

In this example, we define a function named `square` with a parameter `x`. The function body simply multiplies `x` by itself and returns the result. This syntax is more concise and easier to read when you have a simple expression that can be evaluated in a single line.
you can define an anonymous function using a syntax similar to defining a named function, but without providing a name. Here's an example:
```javascript
let square = (x) = x * x
```

In this example, we define an anonymous function that takes one parameter `x` and returns the result of `x * x`. We store this function in a variable named `square`.

Anonymous functions are useful when you need to define a small, one-off function that you don't need to use again later in your code. They can be passed as arguments to other functions, assigned to variables, and used wherever you would use a named function.

Both of these ways will work the same way when you call the function with an argument. For example, if you call the function `square` with an argument of `4`, you will get a result of `16`:
```javascript
let result = square(4)
term.log(result) # Output: 16
```
In this example, we call the square function with an argument of `5` and store the result in a variable named `result`. We log the value of `result` to the console using `term.log`.