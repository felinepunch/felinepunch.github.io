---
title: "Pure Functions in Imperative Programming"
date: 2026-01-04 00:00:00 +0800
categories: [Paradigm]
tags: [pure function]
---

It is translated from Korean to English by Google Translate.

# Meaning and Examples of Pure Functions

Pure functions borrow some of the concept of functions from mathematics. Computers are physical entities, and therefore cannot be identical to ideal mathematics. Unlike mathematics, computers have memory and computational speed limitations, and a numerical range. They also must handle errors.

A pure function is a function that satisfies the following two properties:
* Returns the same value for the same arguments.
* Has no side effects.

In programming, functions have parameters and return values.
When a function is called from outside, its arguments are passed to the function as parameters.
To return the same value for the same arguments, values ​​other than the arguments must not be read and used in calculations.

Readable state outside of arguments
* Mutable shared variables outside of the function
* Input (keyboard, mouse, etc.)
* External reading (files, databases, etc.)
* Variables within a function that persist after the function call ends and can be used in the next function call (static variables in C)

In programming, a side effect of a function refers to anything that affects external state beyond the return value.

Examples of side effects
* Changing the value of a mutable shared variable outside of the function (global variables, etc.)
* Saving to an external location (files, databases, etc.)
* Displaying (screen, printer, network, etc.)

```
function add: (a: integer, b: integer) -> (integer)
    return a + b
end

c: integer = add(3, 5)
display(c) // 8

```
3 and 5 are arguments, and a and b are parameters. The value of a is 3, and the value of b is 5.
The above function satisfies both properties, making it a pure function.

```
function add: (a: integer, b: integer) -> (integer)
    x: integer = a + b
    display(x) // 8
    return x
end

c: integer = add(3, 5)
display(c) // 8

```
The above function is impure and has a side effect: it outputs the value to an external source.

```
count: integer = 0

function add: (a: integer, b: integer) -> (integer)
    x: integer = a + b
    count = count + 1
    return x
end

c: integer = add(3, 5)
display(c) // 8

```
The above function reads and writes the value of a mutable shared variable. It does not affect the return value. However, because it writes to an external mutable shared variable, it is an impure function with a side effect.

```
m: integer = 2

function add: (a: integer, b: integer) -> (integer)
    return a + b + m
end

c: integer = add(3, 5)
display(c) // 10

m = 4
d: integer = add(3, 5)
display(d) // 12

```
The above function has no side effects. However, since it reads the value of a mutable shared variable and uses it in its calculations, the return value may not be the same if the value of the mutable shared variable is changed externally. Therefore, it is an impure function.

```
m: integer = 2

function add: (a: integer, b: integer) -> (integer)
    x: integer = b + m
    return a + b
end

c: integer = add(3, 5)
display(c) // 8

m = 4
d: integer = add(3, 5)
display(d) // 8

```
The above function has no side effects. It reads the value of the mutable shared variable, but does not use it in the calculation to generate the return value. Therefore, it is a pure function. However, it does have some unnecessary behavior.

# Various Implementations of Pure Functions

Functional programming creates programs by composing and combining functions, while pure functional programming creates programs using pure functions and immutable variables in addition to functional programming.
Pure functions and immutable variables are not directly related, except that the arguments of pure functions are immutable. In pure functional programming languages, all variables are immutable, automatically making all functions pure.

```
function sum: (n: integer) -> (integer)
    return n + sum(n - 1)
end

```
This is a traditional functional implementation of pure functions using recursive functions.

```
function sum: (n: integer) -> (integer)
    s: integer = 0
    foreach i in [1, n]
        s = s + i
    end
    return s
end
```
This is an imperative implementation of pure functions. It satisfies both properties of a pure function.
We can see that creating a pure function doesn't require the use of immutable variables.

Let's delve deeper, keeping in mind that even imperative code within a pure function satisfies the properties of a pure function.

```
function concatenate: (a: string, b: string) -> (string)

function concatenate: (array: string[], index: int) -> (string)
    if index = array.length
        return ""
    end
    return concatenate(array[index], concatenate(array, index + 1))
end

function concatenate: (array: string[]) -> (string)
    return concatenate(array, 0)
end
```
This is a pure function that concatenates strings, written in a purely functional manner. All variables are immutable.

```
function concatenate: (array: string[]) -> (string)
    stringBuffer: StringBuffer = StringBuffer()
    foreach element in array
        stringBuffer.Append(element)
    end
    return stringBuffer.ToString()
end

```
This is a pure function that performs the same task, written in an imperative style.
Generally, pure functions are known to maintain purity only when they call other functions within them. However, in the above example, purity was maintained despite the use of an object's method.

Classes that can be used in pure functions must be similar to pure functions, except that all fields are private and public methods can access fields.

Let's look at a sorting example.
```
function sort: (array: integer[]) -> (integer[])
```
This format is a pure function. Since the arguments cannot be modified, it returns a newly created sorted array.

```
function sort: (array: integer[])
```
This format is imperative. Since the arguments can be changed, it sorts the array passed as an argument.

Both sorting functions can be used in pure functions.

Imperative functions that can be used in pure functions are similar to pure functions except that their arguments can be changed.

In imperative languages, both pure functional and imperative modes can be used to implement pure functions.

Pure functional implementations are generally recommended, but if the function is too complex, uses a lot of memory, or is too slow for frequent use, an imperative mode may be preferable.

```
function factorial: (n: integer) -> (integer)
    if n <= 1
        return 1
    end
    return n * factorial(n - 1)
end
```
This is a recursive pure function.
Multiplies the return value of factorial(n - 1) by n. Since the last operation before recursion is a multiplication operation, tail recursion does not apply.

```
function factorial: (n: integer, acc: integer) -> (integer)
    if n = 1
        return acc
    end
    return factorial(n - 1, acc * n)
end

function factorial: (n: integer) -> (integer)
    return factorial(n, 1)
end
```
This is a pure function that uses tail recursion for speed.
However, it is more complex than the recursive version without tail recursion.
Intentionally creating tail recursion in a recursive function is not recommended unless it naturally occurs.
For speed, it's easier to understand if you implement it in imperative form; otherwise, it's easier to understand if you implement it using regular recursion.
The only time tail recursion, even if it's difficult to understand, is when mutable variables aren't available in a purely functional language.

```
function factorial: (n: integer) -> (integer) 
    if n <= 1 
        return 1 
    end 
    acc: integer = 1 
    foreach i in [1, n] 
        acc = acc * i 
    end 
    return acc
end
```