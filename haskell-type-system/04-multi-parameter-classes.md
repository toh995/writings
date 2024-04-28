---
date: 2023-11-27
---
# Multi-Parameter Classes
Sources:
- https://web.cecs.pdx.edu/~mpj/pubs/fundeps.html
- https://wiki.haskell.org/Functional_dependencies

So far, we have dealt with classes of the form
```haskell
class C u where
    foo :: u
```

And constraints like
```haskell
(C a) =>
```

With the following intuitions:
- `C` represents a set of type constructors. In this case, $C \subset (*)$.
- The constraint `(C a) =>` asserts that $a \in C$.

Note - the above class is declared over a *single* type constructor variable `u`.

Now consider a class with *multiple* type constructor variables:
```haskell
class R u v w where
    foo :: u v -> w
```

And a constraint like:
```haskell
(R a b c) =>
```

Here, `R` isn't a set of type constructors.

Instead, `R` is a 3-place **relation**, i.e. a set of 3-tuples.

In this particular case, $R \subset (* \rightarrow *) \times (*) \times (*)$

Also, the constraint `(R a b c) =>`, asserts that $(a, b, c) \in R$.

## Multi-Parameter Constraint Examples
### Functions
Consider the example:
```haskell
(C a b c, D b c d) => foo :: a b -> c d
```

This means that, `foo` is valid for any $a, b, c, d$ such that:
- $a, c \in (* \rightarrow *)$
- $b, d \in (*)$
- $(a, b, c) \in C$
- $(b, c, d) \in D$

### Instance Declarations
Consider the example:
```haskell
instance (Bar a a b, Baz b a) => Foo [a] Int b where
```

This means that, for any $a, b$ such that $(a, a, b) \in Bar$ and $(b, a) \in Baz$, we have $([a], Int, b) \in Foo$.


### Class Declarations
Consider the example:
```haskell
class (Bar a a b, Baz b a) => Foo a b where
    foo :: a b
```

This means that, for any $(a, b) \in Foo$, we MUST have $(a, a, b) \in Bar$ and $(b, a) \in Baz$.

## Functional Dependencies
Here we only explain the syntax and intuition for functional dependencies.

For a more in-depth reading on the motivation and use cases, see [here](https://wiki.haskell.org/Functional_dependencies). There's also a more detailed [academic paper](https://web.cecs.pdx.edu/~mpj/pubs/fundeps.html), which first introduced the concept.

Note that the functional dependencies feature is NOT included in GHC2021, and thus must be explicitly activated via a [language pragma](https://wiki.haskell.org/Language_Pragmas).

### Explanation
Consider the example:
```haskell
class Foo a b c d | a b -> c d where
```

Intuitively, this means that, the parameters `a` and `b` uniquely determine `c` and `d` in the class `Foo`.

More formally:

Let $(a_1, b_1, c_1, d_1), (a_2, b_2, c_2, d_2) \in Foo$.

Then, if $a_1 = a_2$ and $b_2 = b_2$, then $c_1 = c_2$ and $d_1 = d_2$.

### More Examples
```haskell
class C a b where
class D a b | a -> b where
class E a b | a -> b, b -> a where
```

Intuitions:
- `C` is a binary relation.
- `D` is a binary relation that is a function.
- `E` is a one-to-one function.

An additional complex example:
```haskell
class (Bar a a b, Baz b a) => Foo a b | a -> b where
    foo :: a b
```

This means that, for any $(a, b) \in Foo$, ALL of the following must be true:
- **Kind Inference:** $a \in (* \rightarrow *)$ and $b \in (*)$
- **Type Constraint:** $(a, a, b) \in Bar$ and $(b, a) \in Baz$
- **Functional Dependency:** For any $(a, b') \in Foo$, we have $b' = b$
