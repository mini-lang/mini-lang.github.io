---
layout: page
title: Implementing the v1 interpreter
permalink: /tutorial/v1
---

# From syntax to working control flow

This companion to *Write a language in a week* follows the educational v1
interpreter. The earlier chapters introduce language design; this walkthrough
connects those ideas to the code that actually runs. Start with the
[v1 reference](/documentation) and Python 3.10 or later.

## Follow the active path

In the [interpreter repository](https://github.com/mini-lang/mini-lang),
`mini.py` calls `execute` in `src/interpreter.py`. Its lexer and parser imports
are compatibility facades for `src/manual/lexer.py` and `src/manual/parser.py`.
The handwritten parser builds nodes from `src/manual/ast.py`; `src/evaluator.py`
walks them using environments from `src/environment.py`.

The newer `src/grammar/mini.lark` describes proposed syntax. It has no connection
to execution. Adding a production there does not add a working language feature.
Keep [the capability matrix](https://github.com/mini-lang/mini-lang/blob/main/docs/v1.md)
next to you when experimenting. Classes, enums and pattern matching remain exercises
for a future version.

## Make a loop able to change its condition

A block creates a child environment. Reading a variable searches outward through
parents. Assignment must update the nearest existing binding, or a counter changed
inside a loop body would leave the outer condition unchanged. A new name still
belongs to the current scope. `Environment.define` deliberately binds locally for
function parameters and for-loop variables, so those names can shadow outer ones.

```mini
count = 0
while count < 3 {
    count += 1
}
print(count)
outer = 99
for outer in 1..3 { print(outer) }
print(outer)
// => 3
// => 1
// => 2
// => 99
```

The evaluator checks a while condition before each iteration and requires a bool.
A for loop evaluates its collection once. Both loops return `()`. Each iteration
gets a fresh scope, so closures capture separate iteration bindings:

```mini
functions = []
for n in 1..4 {
    list_append(functions, () => n)
}
print(functions[0](), functions[1](), functions[2]())
// => 1 2 3
```

Ranges here are eager lists with an excluded upper bound: `1..4` visits 1, 2, 3.
No lazy iterator or pattern binding machinery is needed for this subset.

## Unwind structured control flow

A normal expression produces a value. A `break`, `continue` or `return` must
instead stop evaluating the rest of its current block. The evaluator uses small
internal exceptions: `LoopControl` and `ReturnControl`.

A loop catches only loop control from its body. `break` exits that loop;
`continue` starts the next iteration. The nearest nested loop catches its own
signal first. Function calls catch return signals and yield their values; return
therefore travels through intervening blocks and loops.

```mini
first_odd(values) = {
    for value in values {
        if value % 2 == 0 { continue }
        return value
    }
    ()
}
print(first_odd([2, 4, 7, 9]), first_odd([]))
// => 7 ()
```

The parser tracks function and loop-body nesting. When parsing a function body it
resets loop depth: a function declared inside a loop cannot break its caller's
loop. This check also rejects invalid control flow in unreachable branches and
uncalled functions. A new loop inside that function may still use loop control.

There is no return-from-arbitrary-block rule. A plain block yields its last
expression; `return expression` is function-only. Because whitespace is ignored,
v1 requires an explicit value: write `return ()` for unit.

## Keep errors separate from control signals

The lexer records each token's start, including source name, line and column.
AST nodes retain the relevant token. The parser raises a syntax `MiniError`;
the evaluator attaches the innermost expression location to runtime failures.
Internal loop/return signals pass through that error wrapper unchanged.

For example, running a file named `bad.m` containing `print(missing)` reports:

```text
bad.m:1:7: Runtime error: identifier 'missing' is not defined
```

`execute` raises `MiniError` for embedding callers. The CLI catches it, writes to
stderr, and returns status 1. Successful file execution returns 0. The REPL catches
errors per input and preserves its environment for the next expression.

## Verify the language and its documentation together

From the interpreter checkout, run:

```sh
python test.py
```

This runs the legacy tests, all example files, scope/control-flow/diagnostic tests,
and every `mini` fence in the README and `docs/`. Expected output is written in
`// =>` comments; each fence is a standalone program. Tests also check failure
status, so a program that merely prints an error cannot pass as a successful run.

To include this companion checkout, point the test runner at it:

```sh
MINI_TUTORIAL_DIR=../mini-lang.github.io python test.py
```

The companion's CI runs the same fence test against the interpreter. Merge these
tutorial changes after the v1 interpreter changes so the checked examples agree.

For another treatment of the same ideas, read *Crafting Interpreters* on
[control flow](https://craftinginterpreters.com/control-flow.html),
[functions](https://craftinginterpreters.com/functions.html), and
[resolving and binding](https://craftinginterpreters.com/resolving-and-binding.html).
Mini has its own expression syntax and scope rules; these chapters explain the
techniques rather than define Mini's behavior.
