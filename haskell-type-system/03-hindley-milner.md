---
date: 2023-11-26
---
# Hindley-Milner Type System (HM)
Sources:
- https://web.cecs.pdx.edu/~mpj/pubs/springschool.html
- https://en.wikipedia.org/wiki/Hindley%E2%80%93Milner_type_system
- https://stackoverflow.com/questions/399312/what-is-hindley-milner

HM provides the following features:
- Type inference
- Unconstrained polymorphism
- Type constructor variables, ONLY for kind `*`

Haskell adds two extensions to HM:
1. Constrained Polymorphism (aka type classes)
2. Type constructor variables for ANY kind (not just `*`)

Notes:
- In HM without extensions, classes don't exist.
- In HM + extension (1), classes exist for `*` type constructors only.
- In HM + extension (1) AND (2), classes exist for arbitrarily-kinded type constructors.

Terminology:
- A **type class** is a class for a `*` type constructor.
- A **constructor class** is a class for any other kinded type constructor.

The two HM extensions are *independent* of each other, however the combination of both extensions is quite powerful - it allows for **constructor classes**.
