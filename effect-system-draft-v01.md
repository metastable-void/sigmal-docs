# Sigmal Generic Effect System (Draft v1)

## 1. Overview

Sigmal does **not** bake any fixed I/O, world, or effect primitives into
the language.

Instead, Sigmal provides a *generic, purely functional effect mechanism*
based on a Free construction over an arbitrary operation signature.

This design ensures:

-   No built-in I/O
-   No ambient global world
-   Freestanding and hosted targets treated uniformly
-   Effects represented as pure data
-   Interpretation performed explicitly via capability values

The language core remains pure and total.

------------------------------------------------------------------------

## 2. Universes

Sigmal writes universes as:

-   `$0` --- ordinary value-level types
-   `$1` --- types of `$0`-types
-   `$2` --- types of `$1`-types
-   etc.

An operation signature `Op` has type:

``` sigmal
Op : $0 -> $0
```

This entire arrow type lives in `$1`.

------------------------------------------------------------------------

## 3. Operation Frame (Explicit Σ-Packed Existential)

To encode existential result types explicitly, Sigmal uses dependent
records.

``` sigmal
OpFrame =
  fn (Op : $0 -> $0) (A : $0) {
    &{
      X  : $0;
      op : Op X;
      k  : (X -> A);
    }
  };
```

This is an explicit Σ-pack:

-   `X` is the hidden intermediate result type.
-   `op` is the operation request producing `X`.
-   `k` is the continuation consuming `X`.

There is no hidden existential mechanism in the language.

------------------------------------------------------------------------

## 4. Generic Free Construction

Effects are represented as a generic Free construction:

``` sigmal
Free =
# (Op : $0 -> $0) (A : $0) {
  Pure(A);
  Op(OpFrame Op (Free Op A));
};
```

Meaning:

-   `Free Op A : $0`
-   `Pure(A)` embeds a pure value.
-   `Op(...)` represents one operation step with continuation.

The language does not assume anything about `Op`.

------------------------------------------------------------------------

## 5. Monad Dictionary for Free

A Monad dictionary is constructed generically for any `Op`.

``` sigmal
FreeMonad =
  fn (Op : $0 -> $0) {
    &{

      pure =
        fn (A : $0) (x : A) {
          Free Op A .Pure x
        };

      bind =
        fn (A : $0) (B : $0)
           (m : Free Op A)
           (f : (A -> Free Op B)) {

          match m {

            Free.Pure(x) =>
              f x;

            Free.Op(fr) =>
              Free Op B .Op
                &{
                  X  = fr.X;
                  op = fr.op;
                  k  = fn (x : fr.X) {
                         FreeMonad Op .bind
                           fr.X
                           B
                           (fr.k x)
                           f
                       };
                };
          }
        };
    }
  };
```

This definition:

-   Is fully explicit
-   Uses no hidden existential tricks
-   Elaborates cleanly to Π/Σ core

Downcasts to Applicative and Functor work via previously defined record
conversions (`monadToApplicative`, `monadToFunctor`).

------------------------------------------------------------------------

## 6. Generic Handler (Capability Interpreter)

Interpretation of operations is performed by a handler value.

``` sigmal
Handler =
  fn (Op : $0 -> $0) {
    &{
      handle =
        fn (X : $0) (op : Op X) -> X;
    }
  };
```

This is the only bridge to the outside world.

The language itself does not define any particular handler.

------------------------------------------------------------------------

## 7. Running Free

``` sigmal
runFree =
  fn (Op : $0 -> $0)
     (A  : $0)
     (h  : Handler Op)
     (p  : Free Op A) {

    match p {

      Free.Pure(x) =>
        x;

      Free.Op(fr) =>
        runFree
          Op
          A
          h
          (fr.k (h.handle fr.X fr.op));
    }
  };
```

Interpretation is explicit and capability-driven.

------------------------------------------------------------------------

## 8. Example: User-Defined Console Signature

A freestanding developer may define:

``` sigmal
ConsoleOp =
# (X : $0) {
  PutByte(U8);   // returns Unit
  GetByte;       // returns U8
};
```

Console effect type:

``` sigmal
Console = fn (A : $0) { Free ConsoleOp A };
```

Kernel-level handler:

``` sigmal
consoleHandler =
  &{
    handle =
      fn (X : $0) (op : ConsoleOp X) {
        match op {
          ConsoleOp.PutByte(b) => { hw.serial.put b; () };
          ConsoleOp.GetByte    => hw.serial.get ();
        }
      };
  };
```

Hosted WASM code may provide a different handler that uses imports.

The effect system remains unchanged.

------------------------------------------------------------------------

## 9. Design Guarantees

✔ No baked-in I/O\
✔ No fixed world type\
✔ Effects are pure data\
✔ Interpretation is explicit\
✔ Freestanding environments can define arbitrary operations\
✔ Hosted targets can provide handlers via capability modules\
✔ Universe levels `$N` remain explicit

------------------------------------------------------------------------

# End of Document
