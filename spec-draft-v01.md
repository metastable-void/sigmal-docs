# Sigmal Language Specification (Draft v1)

## 1. Overview

Sigmal is a total, pure, statically typed, compiled programming
language.

Core principles:

-   No implicit definitions
-   No global namespace
-   Files evaluate to module (record) values
-   `=` is the only binding operator
-   `:` is used exclusively for typing
-   `.` is used exclusively for projection
-   No tracing garbage collector
-   Explicit capability values for effects
-   Stable standard library names across all targets

Sigmal compiles to LLVM and WASM with minimal runtime components.

------------------------------------------------------------------------

## 2. Binding Model

### 2.1 Single Binding Form

All names are introduced using:

``` sigmal
name = expression;
```

There are no implicit bindings. There is no `def` keyword. There are no
declaration forms separate from expressions.

### 2.2 Files as Records

A file evaluates to a record value.

Example file:

``` sigmal
& {
  x = 10;
  y = 20;
}
```

This is a record literal.

There is no top-level namespace outside the file's record value.

------------------------------------------------------------------------

## 3. Core Syntax Rules

### 3.1 Typing

`:` is used exclusively for typing:

``` sigmal
x : T = value;
```

There is no other meaning for `:`.

### 3.2 Projection

`.` performs record/module projection:

``` sigmal
module.field
```

There is no `::` operator.

Projection is left associative:

``` sigmal
a.b.c
```

means:

``` sigmal
(a.b).c
```

### 3.3 Function Application

Application is whitespace-based:

``` sigmal
f x y
```

means:

``` sigmal
((f x) y)
```

Parentheses are for grouping only.

------------------------------------------------------------------------

## 4. Inductive Types (ASCII Form)

There are no `struct` or `enum` keywords.

Inductive types use ASCII symbolic syntax.

Example:

``` sigmal
MyEnum = # {
  True;
  False;
};
```

The `#` form constructs an inductive type.

### 4.1 Type Projection

`.$` refers to the type constructor:

``` sigmal
t : MyEnum.$ = MyEnum.True;
```

Constructors are projected via `.`:

``` sigmal
MyEnum.True
```

There is no implicit global constructor namespace.

------------------------------------------------------------------------

## 5. Records

Record values use `& {}`:

``` sigmal
Point = & {
  x = 0;
  y = 0;
};
```

Records are first-class values. Modules are records. There is no
distinction between modules and records.

------------------------------------------------------------------------

## 6. Memory Model

### 6.1 No Garbage Collection

Sigmal has no tracing garbage collector.

### 6.2 Ownership Modes

-   Value types (unboxed)
-   Owned heap values
-   Reference-counted values (`Rc`, optional `Weak`)

Reference counting is deterministic and semantically unobservable.

### 6.3 Allocator Interface

The allocator interface lives in `std.alloc`:

``` sigmal
Allocator = & {
  alloc = fn (size: USize, align: USize) -> Ptr<U8> { ... };
  free  = fn (ptr: Ptr<U8>, size: USize, align: USize) -> Unit { ... };
};
```

`std.alloc.default` exists on all targets.

For `wasm32-unknown-unknown`, it is implemented internally using linear
memory.

------------------------------------------------------------------------

## 7. Capability-Based Effects

Effects are modeled using explicit capability values.

Interfaces are defined in `std.core`.

Example:

``` sigmal
IO = & {
  write = fn (Bytes) -> Unit;
  read  = fn () -> Bytes;
};
```

Implementations are provided in `std.cap`.

Example:

``` sigmal
std.cap.io.default
```

Programs consume capability values explicitly:

``` sigmal
greet = fn (io: std.core.IO) {
  io.write (encodeUtf8 "Hello");
};
```

There is no ambient global effect.

------------------------------------------------------------------------

## 8. Standard Library Structure

Stable across all targets:

-   `std.core` --- Pure logical foundation
-   `std.low` --- Freestanding systems primitives
-   `std.alloc` --- Allocator interface and default allocator
-   `std.cap` --- Capability implementations
-   `std.abi` --- ABI definitions
-   `std.high` --- High-level functional utilities

Names remain identical across targets.

------------------------------------------------------------------------

## 9. ABI and Target Model

### 9.1 Export

``` sigmal
export "entry" = functionValue;
```

Only functions may be exported in `wasm32-unknown-unknown`.

### 9.2 Import

``` sigmal
import "host.func" : (U32 -> Unit);
```

Imports bind external symbols to local names.

### 9.3 wasm32-unknown-unknown

-   Exports functions only
-   No implicit imports
-   Default allocator provided internally
-   Zero external dependency by default

------------------------------------------------------------------------

## 10. Comments

Supported comment forms:

-   `//` line comment
-   `/* ... */` nested block comment
-   `///` documentation comment
-   `/** ... */` documentation block

------------------------------------------------------------------------

## 11. Invariants

1.  `=` is the only binding operator.
2.  `:` is only used for typing.
3.  `.` is only projection.
4.  No implicit definitions.
5.  No `struct` or `enum` keywords.
6.  Inductive types use ASCII symbolic form (`#`).
7.  No implicit global namespace.
8.  Standard library names are stable across targets.

------------------------------------------------------------------------

# End of Specification Draft v1
