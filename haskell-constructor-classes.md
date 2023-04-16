# Haskell Contstructor Classes
Some personal notes and revelations, adapted from http://web.cecs.pdx.edu/~mpj/pubs/springschool.html

## Functions, Type Constructors, and Kinds

### Functions
In Haskell, every value is a **function**. Even primitive values like:
```haskell
myNum :: Integer
myNum = 122
```

Technically, `myNum` is a function that accepts 0 arguments.

We can also have functions which accept other functions as arguments. For example, the standard `map` function has type signature:
```haskell
map :: (a -> b) -> [a] -> [b]
```

Which means that, the first argument of `map` must be a function of type `a -> b`.

So really, in Haskell, a function is simply a computation which takes in functions as input, and produces a function as output.

### Type Constructors

#### Types
A **type** represents a set of functions.

For example:
- The `Bool` type is a finite set of two functions (i.e. `true` and `false`)
- The `Integer` type is an infinite set of functions (i.e. `1`, `2`, `3`, ...)
- The `Integer -> Bool` type is a set of all functions, that take in one `Integer` as input, and output a `Bool` (i.e. `even`, `odd`, etc.)

Note that, every function MUST be assigned to a type.

#### Type Constructor Definition/Examples
First, let's revisit our earlier definition of **function**:

> a function is simply a computation which takes in functions as input, and produces a function as output.

A **type constructor** has a very similar definition:
1. A type constructor is a computation which takes in type constructors as input, and produces a type constructor as output.
2. If a type constructor takes 0 arguments, then we call it a **type** (i.e. as defined above)

Example type constructors:
- `Bool` is a type constructor with 0 arguments.
- `Integer` is a type constructor with 0 arguments.
- `[]` is a type constructor with 1 argument.
- `[Bool]` is a type constructor, obtained from the `[]` type constructor, with the first argument as `Bool`.
  - `[Bool]` is a type (i.e. a type constructor with 0 arguments)
  - `[Bool]` represents the set of all boolean lists.
- `->` is a type constructor with 2 arguments.
- `Integer -> Bool` is a type constructor, obtained from the `->` type constructor, with the first argument as `Integer`, and the second argument as `Bool`.
  - `Integer -> Bool` is a type (i.e. a type constructor with 0 arguments)
  - `Integer -> Bool` represents the set of all functions from `Integer` to `Bool`

#### Naming Convention
In Haskell, we use camelCase to refer to **functions**, and PascalCase to refer to **type constructors**.

#### Comparison to Other Languages
In other languages such as Java or TypeScript, we can define new **type constructors** by using generic/parametrized types.

For example:
```typescript
// A type constructor with two arguments
type MyObj<T, U> = {
  prop1: T,
  prop2: U,
}
```

### Kinds
As we saw above, **type constructors** can vary in the number of arguments they accept. For example, `Bool` accepts 0 arguments, while `[]` accepts
1 argument.

A **kind** represents the number of arguments for a type constructor.

Examples:
- `Bool` has kind `*` (since it accepts 0 arguments)
- `[]` has kind `* -> *`
- `->` has kind `* -> * -> *`
- `StateT` has kind `* -> (* -> *) -> * -> *`

Each type constructor MUST be assigned to a **kind**.

We can also think of a **kind** as a set of type constructors:
- `*` represents the set of all type constructors with no arguments
- `* -> *` represents the set of all type constructors with 1 argument
- etc...

In Haskell, we never explicitly assign a kind in source code; instead, the Haskell compiler/interpreter is smart enough to infer the kind on its own.

### Summary
We can essentially think of **functions**, **type constructors**, and **kinds** as three levels of categorization:
- Each **function** must be assigned to a **type constructor** with 0 arguments
- Each **type constructor** must be assigned to a **kind**

Another intuition - a type constructor MUST have kind `*`, before you can assign it to a function.
