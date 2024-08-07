# Functions

## Named Functions
Named functions in Flan are defined using the `func` keyword.
```js
func greet(name) {
    println("Hello, " + name + "!")
}
```

Functions in Flan are first class values and can be assigned to variables, passed to functions, or anything else you might do with any other data type.
```js
// This function takes a function as an argument
func twice(f, x) {
    f(f(x))
}

func add_one(x) x + 1

func add_two(x) twice(add_one, x)
```

### Default Arguments
If a function is not passed a sufficient amout of arguments, it becomes `null` by default:
```js
func some_func(a, b) {}
some_func(:hello) // a = :hello and b = null
```
There is [`default`](./library/std.md#defaultx-base) function in [`std` module](./library/std.md).
```js
{default: default} := import("std")

func some_func(a, b) {
    b = default(b, "Hello")
}
```

### Rest Parameters
You can add `+` after parameter name if you are unsure about the number of arguments to pass in the functions.
```js
{each: each} := import("std")

func sum(nums+) {
    reduce(nums, 0) <| func(accumulator, elem) accumulator + elem
}

sum(1, 4, 5, 3, 6)
```

### Unpacking Argument Lists
The reverse situation occurs when the arguments are already in a list but need to be unpacked for a function call requiring separate arguments.
For instance, the [`range()`](./library/std.md#rangestart-end-step) function expects separate `start` and `end` arguments.
If they are not available separately, write the function call with the `...` operator to unpack the arguments out of a list.
```js
{range: range} := import("std")

args := [1, 5]
range(...args) // [1, 2, 3, 4]
```

## Pipe Operator
Flan provides syntax for passing the result of one function to the arguments of another function, the pipe operator (`|>`).
This is similar in functionality to the same operator in Elixir or F#.

The pipe operator allows you to chain function calls without using a plethora of parenthesis.
```js
names := []
append(append(append(append(names, "Nobu"), "Anna"), "Damian"), "Thomas")
names // ["Nobu", "Anna", "Damian", "Thomas"]
```
This can be expressed more naturally using the pipe operator, eliminating the need to track parenthesis closure.
```js
names
|> append("Nobu")
|> append("Anna")
|> append("Damian")
|> append("Thomas")
```
Each line of this expression applies the function to the result of the previous line.

## Callback Functions
A callback function is a function passed into another function as an argument, which is then invoked inside the outer function.
```js
std := import("std")
std.range(0, 11) |> std.each() <~ (elem, index) {
    // do something
}

// same as 
std.range(0, 11) |> std.each() <| func(elem, index) {
    // do something
}
```
If there are no arguments, the `()` may be omitted.
```js
std.if(true == true) <~ {
    // do something
}
```

## Decorators
Decorators are wrapper functions that modify the functionality of other functions.

This is an example of an example.
```js
{upper: upper} := import("strings")

func shout(f) {
    func (name) {
        f(upper(name) + "!")
    }
}

@[shout]
func print_name(name) {
    println(name)
}

print_name("Nobu") // prints out "NOBU!"
```

## Anonymous Functions
Anonymous functions can be defined with a similar syntax:
```js
func run() {
    add := func(x, y) x + y

    add(1, 2) // 3
}
```
Another way to define an anonymous function:
```js
add := -> (x, y) x + y
```
`()` maybe omitted if there are no arguments. And this is often used for lazy evaluated values.

## Public Functions
Functions annotated with `public` keyword are public functions, meaning they could be exported outside of their own module to be used by other modules.
```js
public func public_func(xs+) {}
```
