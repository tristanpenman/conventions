# Conventions

Coding conventions that I use for my personal projects.

These conventions are designed to be human-readable and useful for coding assistants. It covers language conventions, as well as integrations with third-party frameworks.

---

## ASM

The [asm.md](asm.md) conventions currently cover:

* 6502 Assembly (.s)

## C++

The [cpp.md](C++) conventions currently include the following areas:

* Core Guidelines
  * Files and formatting
  * Naming
  * Headers and includes
  * Namespaces
  * Types, ownership, and interfaces
  * Comments and documentation
  * Example
* Integrations
  * CUDA
  * Oxygine
  * Box2D
  * Dear ImGui
  * Win32
  * Qt

## JavaScript

The [javascript.md](javascript.md) conventions currently cover:

* Core Guidelines
  * Code Style
  * Data and Asynchronous Code
  * Browser and UI Code
  * Example
* Integrations
  * Vue.js
  * React
* Tooling
  * Project and Tooling Choices
  * CSS Conventions
* Testing

## VHDL

The [vhdl.md](vhdl.md) conventions currently cover:

* Core Guidelines
  * Code Style
  * Naming
  * Example
* Best Practices
  * Prefer IEEE `numeric_std`
  * Clock enables instead of deriving clocks from data bits
  * De-duplicate common logic with reusable packages and generics
  * Strengthen verification
