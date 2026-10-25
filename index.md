---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---

<div align="center">
    <br>
    <img alt="Mini-Lang logo" src="assets/logo_small.png" width="430px"/>
    <p>
    <br>
        <b>The mini programming language</b> built for the<br>
        <a href="https://www.mini-lang.org/blog" target="_blank">
            <em>Write a language in a week</em>
        </a> series.
    </p>
    <br>
</div>

# The `mini` language

This is the official programming language `mini` website, designed in the [*Write a language in a week*](https://www.mini-lang.org/blog) blog series.

`mini` is a minimal programming language that is built for the purpose of teaching programming language design.
The language is dynamically typed and expression-based. It is designed to be easy to learn and use. The intended design supports both functional and object oriented programming paradigms.
Read the [full language design](/documentation) for that direction.
The current interpreter implements an educational v1 subset: see the
[verified v1 reference](/documentation/v1) and [implementation tutorial](/tutorial/v1).

It is not intended to be a full language, but rather a language that can be expanded upon and explore new and more advanced concepts in language design.

<br/>

## Installation

The latest version of `mini` can be installed using the commands below:
```sh
git clone https://github.com/mini-lang/mini-lang
cd mini-lang
python mini.py examples/hello_world.m
python mini.py repl
```

> `mini` requires [Python](https://www.python.org/) version **3.10 or later**, with no third-party Python dependencies.

<br/>
