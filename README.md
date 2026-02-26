# ✨ Why Sigmal?

## **🔒 1. Total by design**

Sigmal programs **always terminate**.
No unbounded loops. No undefined behavior. No surprises.

* Deadlock-free
* Safe compile-time evaluation
* Strong optimization opportunities
* Logical consistency by construction

If a computation isn’t total, it simply isn’t Sigmal.

---

## **🧼 2. Pure and deterministic**

Every Sigmal function behaves like a mathematical function:

> Same input → Same output
> Every time.

No hidden state. No mutation. No uncontrolled effects.

Real-world effects (I/O, state, async) are expressed through **explicit effect layers** built on top of Sigmal’s safe core.

---

## **🧩 3. Tiny symbolic core, massive expressiveness**

The entire Sigmal core consists of just five symbolic constructs:

* `\` — functions
* `^` — dependent function types
* `&` — dependent records
* `?` — pattern matching
* `#` — inductive types

From these pieces, Sigmal builds:

* generics
* typeclasses
* modules
* interfaces
* proof systems
* algebraic effects
* high-level surface syntax

All as elaborations of this minimal calculus.

---

## **📦 4. First-class modules**

Modules in Sigmal are **ordinary values** — not compiler magic.

A module is just a Σ-type record.
A functor is just a function from modules to modules.
A macro is just a module-transforming function.

This unlocks:

* typed, safe macros
* code generators as normal functions
* module-level reasoning
* reusable component systems
* trait and interface derivation
* DSLs as libraries, not language extensions

Sigmal treats meta-programming as programming.

---

## **🚀 5. Compiled for performance**

The core of Sigmal erases to a clean, low-level IR with predictable memory layout.

Sigmal can target optimized native code with:

* LLVM
* WebAssembly
* custom backends
* verified kernels

Totality + purity + explicit memory models make low-level optimization safer than ever.

---

## **🧠 6. Logic meets engineering**

Sigmal is designed for developers who want the confidence of formal systems without the heaviness.

* Dependent types without ceremony
* Proofs as ordinary values
* Modules as types and data
* Compile-time computation guaranteed to end

Sigmal code can be both **proved** and **deployed**.

---

# 🧱 The Sigmal Core is Simple

Example snippets in raw Sigmal Core:

### Inductive type

```lisp
(# Bool () Type0
   (True  Bool)
   (False Bool))
```

### Function

```lisp
(\ (b Bool)
   (? b
      (True  False)
      (False True)))
```

### Module (record)

```lisp
(& (T   i32)
   (zero 0)
   (add (\ (x i32) (\ (y i32) (+ x y)))))
```

Everything else is built on top of these constructs.

---

# 🧭 Who is Sigmal for?

* **Systems programmers** who want memory-safety, strong reasoning guarantees, and predictable performance
* **PL researchers** who want a clean total core for experimentation
* **Engineers building DSLs** and compilers
* **Formal method practitioners** who want a practical, useable foundation
* **Anyone frustrated with accidental complexity** in modern languages

---

# 🌱 A language you can trust

Sigmal’s promise is simple:

> **Small. Pure. Total. Extensible.
> A language you can reason about.
> A language you can optimize.
> A language you can grow.**

---

# 🧭 Start exploring Sigmal

Sigmal is new, experimental, and growing.
Join us as we build a language that treats **correctness**, **clarity**, and **expressiveness** as first-class citizens.

👉 Documentation
👉 Examples
👉 GitHub
👉 Community

---

sigmal.org — *the calculus we can build on*
