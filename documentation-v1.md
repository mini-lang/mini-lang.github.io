---
layout: page
title: Educational v1 reference
permalink: /documentation/v1
---

# Educational v1 reference

Mini is a small, dynamically typed, expression-based interpreter for learning
language implementation. This page describes the **handwritten interpreter**,
which implements a subset of the [intended language design](/documentation).

Use Python **3.10 or later**. No third-party Python packages are required.

```sh
git clone https://github.com/mini-lang/mini-lang
cd mini-lang
python mini.py examples/hello_world.m
python mini.py repl
python test.py
```

The CLI loads `src/parser.py` and `src/lexer.py`, which re-export `src/manual/`.
`src/evaluator.py` executes the resulting AST. `src/grammar/mini.lark` is an
experimental grammar draft, **not the active parser**. In particular, its colon
bodies, newline rules and pattern syntax do not describe v1.

See the [capability matrix](https://github.com/mini-lang/mini-lang/blob/main/docs/v1.md)
and [implementation tutorial](/tutorial/v1). Every `mini` code fence below runs in
a fresh environment in the interpreter's documentation tests; `// =>` gives the
exact printed output.

## Values and expressions

Values include numbers, booleans, strings, lists, tuples, maps and unit `()`.
Operators include `+ - * / % ^`, comparisons, and short-circuit `and` / `or`
(also `&&` / `||`). Negation is `not` or `!`; single `&` and `|` are unsupported.
`(x)` groups an expression; `(x, y)` is a tuple. A one-element tuple literal is
not supported. Unicode letters are allowed in identifiers; emoji belong in strings.

Whitespace separates expressions. Newlines are not statement terminators;
semicolons are unsupported. Keep calls and indexes adjacent to their receiver:
`f(x)`, `items[0]`. Comments use `//` or `/* ... */`.

```mini
π = 3.14
print(π * 2, not false, "hello 🌍")
print(false and missing)
// => 6.28 true hello 🌍
// => false
```

## Variables, blocks and functions

`=` creates a local name if no enclosing binding exists; otherwise it updates
the nearest existing binding. Identifier `+=` is supported for numbers and
strings. Other compound assignments are deferred. Underscore-prefixed names
are ordinary variables, not discards.

Blocks return their last expression, or `()` if empty. New block-local names
do not leak. Functions capture their defining environment, and parameters always
bind locally, shadowing outer names. Functions may use an expression or block body.

```mini
x = 10
{ x += 2 local = 99 }
add(x, y) = x + y
square = (x) => x * x
print(x, add(2, 3), square(4))
// => 12 5 16
```

## Collections and built-ins

Lists concatenate with `+`; tuples add element-wise; maps merge with `+`.
Indexing supports lists, tuples and maps, including negative list/tuple indexes.
Lists and maps support assignment through computed indexes. Map identifiers are
literal string keys. Integer and boolean literal keys use their string forms.
Slices require explicit start and end; the end is excluded and the step cannot be zero.

Use free functions such as `list_append`, `list_size`, `list_map`, `list_filter`,
`list_reduce`, `map_size` and `map_contains`. Collection methods such as
`items.append(value)` are not implemented. `print`, `input`, `typeof` and
`assert(condition, message)` are also available.

```mini
items = [1, 2, 3]
for i in 0..3 { items[i] = items[i] * 2 }
list_append(items, 8)
print(items, items[1:3])
m = #{name: "Mini"}
key = "name"
m[key] = "Ada"
print(m.name, map_size(m))
// => [2, 4, 6, 8] [4, 6]
// => Ada 1
```

The conversion functions are `str`, `number`, `int` and `bool`, not `string`.
`number` and `int` return `()` when string conversion fails. The existing `bool`
conversion recognizes the text `"true"` case-insensitively; it is not general
truthiness. Conditions themselves must be booleans.

```mini
print(str(42), number("1.5"), int("12"), bool("true"), bool(1))
print(number("invalid"))
// => 42 1.5 12 true false
// => ()
```

The larger host-oriented standard library is experimental and outside the v1
example guarantee.

## Control flow structures

`if condition { ... } else if condition { ... } else { ... }` returns the chosen
body's value. Without a matching branch or `else`, it returns `()`.
Use braces for clear body boundaries; v1 also retains single-expression bodies
without colons.

`while condition { ... }` checks a boolean before every iteration.
`for name in values { ... }` evaluates the iterable once and visits list/tuple
elements, string characters or map keys in insertion order. Unit `()` iterates
as an empty tuple. Each iteration has a fresh scope; the for name shadows an
outer name and does not leak. Loops return `()`.

```mini
n = 0
while n < 4 {
    n += 1
    if n == 2 { continue }
    print(n)
}
for key in #{a: 1, b: 2} { print(key) }
// => 1
// => 3
// => 4
// => a
// => b
```

`break` exits the nearest loop; `continue` skips to its next iteration.
Neither may cross a function boundary. `return expression` exits the current
function through nested blocks and loops. Use `return ()` for unit. Return from
a top-level block, and loop control outside a loop body, are errors even when
that code would never run.

## Ranges

`start..end` produces an **eager, ascending, end-exclusive integer list**.
Both bounds are required. This is not the older proposed inclusive lazy range.

```mini
print(1..4)
print(4..1)
// => [1, 2, 3]
// => []
```

## Errors and deferred features

Lexical, syntax and runtime errors include the filename and one-based line and
column. File execution writes errors to stderr and exits nonzero. The REPL
reports an error and accepts the next input; EOF exits it. `--debug` includes
Python tracebacks for interpreter development.

Classes, enums, pattern matching (`match`, `is`, destructuring), sets, modules,
membership `in` outside a for header, open-ended ranges and omitted slice bounds
are deferred. The grammar draft's `none` is unsupported; use `()`.
Consult the [TODO list](https://github.com/mini-lang/mini-lang/blob/main/TODO.md)
before extending the language.
