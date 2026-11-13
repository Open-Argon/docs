# Classes and objects

Classes are real objects in Chloride, with inheritance and introspection.

## Defining a class

```argon
class Animal do
    this.__init__(self, name) = do
        self.name = name

    this.speak(self) = `$(self.name) makes a sound`

let pet = Animal("Rex")
term.log(pet.speak())   # Rex makes a sound
```

Inside the class body, `this` refers to the class being defined, so
`this.method(self, ...) = ...` defines a method on it. Calling the class creates an
instance and passes the arguments to `__init__`. The first parameter of a method receives
the instance and is named `self` by convention.

Instance attributes are created by assigning to `self` (usually in `__init__`), and can be
added or removed at any time:

```argon
class Point do
    this.__init__(self, x, y) = do
        self.x = x
        self.y = y

let p = Point(1, 2)
p.z = 3
delete p.z
term.log(p.x, p.y)   # 1 2
```

Reading an attribute that doesn't exist throws an error.

The class body can be any expression. Use an empty `do`, or `null`, for a class with no
body of its own:

```argon
class Empty do
class AlsoEmpty(Exception) null
```

## Inheritance

Put the parent class in parentheses after the class name. Methods that aren't overridden
are inherited, and a parent's version can be called explicitly through the parent class:

```argon
class Animal do
    this.__init__(self, name) = do
        self.name = name
    this.speak(self) = `$(self.name) makes a sound`

class Dog(Animal) do
    this.speak(self) = `$(self.name) barks`
    this.speak_generic(self) = Animal.speak(self)

let d = Dog("Rex")
term.log(d.speak())           # Rex barks
term.log(d.speak_generic())   # Rex makes a sound
term.log(is_instance(d, Animal), is_instance(d, Dog))   # true true
```

A class without a parent inherits from `object`.

## Class attributes and static methods

Attributes assigned with `this.name = value` belong to the class. They're shared by every
instance, and can be read through an instance or the class itself:

```argon
class Counter do
    this.created = 0
    this.__init__(self) = do
        Counter.created += 1

Counter()
Counter()
term.log(Counter.created)   # 2
```

Class attributes and methods are inherited: a subclass and its instances can read them
too. Assigning through the subclass (`Child.created = 1`) creates a separate attribute on
the subclass rather than changing the parent's.

A method defined **without** a `self` parameter is a static method, called on the class.
Inside it, `this` still refers to the class, so `this(...)` creates an instance:

```argon
class Temperature do
    this.__init__(self, celsius) = do
        self.celsius = celsius
    this.from_fahrenheit(f) = this((f - 32) * 5 / 9)

term.log(Temperature.from_fahrenheit(212).celsius)   # 100
```

## Private members

Variables declared with `let` inside the class body are private: methods can use them, but
they aren't attributes of the class or its instances.

```argon
class Secretive do
    let secret = "hidden"
    this.reveal(self) = secret

term.log(Secretive().reveal())   # hidden
# Secretive.secret and Secretive().secret both throw
```

## Properties: `get_` and `set_`

A method named `get_<name>` is called when the attribute `<name>` is read, and
`set_<name>` is called when it is assigned. This is how built-in properties like `.length`
work:

```argon
class Temperature do
    this.__init__(self, celsius) = do
        self.celsius = celsius
    this.get_fahrenheit(self) = self.celsius * 9 / 5 + 32
    this.set_fahrenheit(self, value) = do
        self.celsius = (value - 32) * 5 / 9

let t = Temperature(100)
term.log(t.fahrenheit)   # 212
t.fahrenheit = 32
term.log(t.celsius)      # 0
```

## Operator overloading (dunder methods)

Classes customise how they work with operators and built-in functions by defining
"dunder" (double-underscore) methods. The built-in types use exactly the same mechanism.

| Method | Called for |
|---|---|
| `__init__(self, ...)` | `MyClass(...)`, to initialise a new instance |
| `__new__(cls, ...)` | `MyClass(...)`, to create the instance (rarely needed, see below) |
| `__string__(self)` | `string(obj)`, `term.log(obj)`, template strings |
| `__repr__(self)` | how the object appears inside a printed array or dictionary |
| `__number__(self)` | `number(obj)` |
| `__boolean__(self)` | truthiness: `if (obj)`, `!obj`, `boolean(obj)` |
| `__add__`, `__subtract__`, `__multiply__`, `__division__`, `__floor_division__`, `__modulo__`, `__exponent__` | `+`, `-`, `*`, `/`, `//`, `%`, `^` (with `(self, other)`) |
| `__negation__(self)` | unary `-` |
| `__equal__`, `__not_equal__` | `==`, `!=` |
| `__less_than__`, `__less_than_equal__`, `__greater_than__`, `__greater_than_equal__` | `<`, `<=`, `>`, `>=` |
| `__contains__(self, item)` | `item in obj` |
| `__getitem__(self, key)` | `obj[key]` |
| `__setitem__(self, key, value)` | `obj[key] = value` |
| `__delitem__(self, key)` | `delete obj[key]` |
| `__getattr__(self, name)` | reading an attribute that doesn't exist |
| `__setattr__(self, name, value)` | assigning any attribute |
| `__call__(self, ...)` | calling the object like a function: `obj(...)` |
| `__iter__(self)` / `__next__(self)` | `for` loops and `array(obj)` (see [iterators](../control-flow/for/README.md#the-iterator-protocol)) |
| `__hash__(self)` | using the object as a dictionary key (return a number) |
| `__template__(self, parts)` | [tagged template strings](../strings/README.md#tagged-templates) |

```argon
class Vector do
    this.__init__(self, x, y) = do
        self.x = x
        self.y = y
    this.__add__(self, other) = Vector(self.x + other.x, self.y + other.y)
    this.__multiply__(self, k) = Vector(self.x * k, self.y * k)
    this.__equal__(self, other) = self.x == other.x && self.y == other.y
    this.__negation__(self) = Vector(-self.x, -self.y)
    this.__boolean__(self) = self.x != 0 || self.y != 0
    this.__string__(self) = `Vector($(self.x), $(self.y))`
    this.__repr__(self) = `<$(self.x), $(self.y)>`

let a = Vector(1, 2)
let b = Vector(3, 4)
term.log(a + b, a * 3, -a)          # Vector(4, 6) Vector(3, 6) Vector(-1, -2)
term.log(a == Vector(1, 2))         # true
term.log(boolean(Vector(0, 0)))     # false
term.log([a, b])                    # [<1, 2>, <3, 4>]
```

### Customising construction with `__new__`

Calling a class first calls `__new__(cls, ...)` to create the instance and then
`__init__(self, ...)` to initialise it. To override `__new__`, call `super.__init__(cls, ...)`
to get a new instance, then return it:

```argon
class Tracked do
    this.__new__(cls, *args) = do
        let instance = super.__init__(cls, *args)
        instance.tracked = true
        return instance

term.log(Tracked().tracked)   # true
```

## Introspection

| Expression | Value |
|---|---|
| `type(obj)` | the object's class |
| `type(obj).__name__` / `MyClass.__name__` | the class name as a string |
| `MyClass.__base__` | the class's parent class |
| `is_instance(obj, MyClass)` | whether `obj` is an instance of `MyClass` or a subclass |

```argon
class Animal do
class Dog(Animal) do
let d = Dog()
term.log(type(d) == Dog, type(d).__name__, Dog.__base__ == Animal)   # true Dog true
```

## Next

- [Errors and exceptions](../error-handling/README.md)
