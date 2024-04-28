---
date: 2023-11-25
---
# Polymorphism
Sources:
- http://web.cecs.pdx.edu/~mpj/pubs/springschool.html
- https://wiki.haskell.org/Polymorphism
- https://en.wikibooks.org/wiki/Haskell/Classes_and_types
- https://en.wikipedia.org/wiki/Polymorphism_(computer_science)

Generally, **polymorphism** refers the usage of a single symbol to represent multiple different types.

In OOP, this usually refers to "subtyping", i.e. a superclass can be substituted with any of its subclasses.

However, according to [wikipedia](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)), there are two other major categories of polymorphism:
- Parametric polymorphism
- Ad-hoc polymorphism

## Variables
To achieve polymorphism properly in Haskell, we use **variables**.

A **variable** is a symbol that can take on multiple possible values.

Variables are constrasted with **literals**, which represent exactly one value.

Let's look at some examples - we can consider both **function** variables and literals, and **type constructor** variables and literals.

### Functions
Consider the example:

```haskell
fib :: Int -> Int
fib 0 = 0
fib 1 = 1
fib n = fib (n-1) + fib (n-2)
```

Here, we define a new function literal `fib` $\in (Int \rightarrow Int)$.

Also, the symbols `0`, `1` $\in Int$, are function literals.

The symbol `n` is a function variable. `n` can take on any function belonging to the set $Int \setminus \lbrace 0, 1 \rbrace$

### Type Constructors
Consider the following example:
```haskell
length :: Foldable t => t a -> Int 
```

Here we have one type constructor literal, `Int` $\in (*)$.

The other symbols, `t` and `a`, are type constructor variables.

`t` can take on any type constructor in the set $Foldable \subset (* \rightarrow *)$.

`a` can take on any type constructor in the set $(*)$.

In Haskell, type constructor **literals** MUST start with an uppercase letter, while type constructor **variables** MUST start with a lowercase letter.

Idiomatically, type constructor variables are single lowercase letters.

## Parametric Polymorphism
Parametric polymorphism refers to the usage of _unconstrained_ type constructor variables.

For example:
```haskell
id :: a -> a
```

In this case, `a` can refer to any arbitrary type, without restriction. i.e. $a \in (*)$.

Note that, if the same type constructor variable appears multiple times, then it must take the same type everywhere it appears.

Another example:
```haskell
length :: [a] -> Integer
```

Also note that, so far, our type variables have kind `*`. However, it is possible to have type constructor variables of other kinds, for example:
```haskell
sum :: t Integer -> Integer
```

Here, `t` has kind `* -> *`.

## Ad-hoc Polymorphism
Ad-hoc polymorphism refers to the usage of _constrained_ type constructor variables.

It also allows us to define _function overloads_, i.e. a single function which has multiple implementations.

To acheive ad-hoc polymorphism, we use **classes**.

### Classes
In Haskell, the words **class** and **instance** have slightly different meanings compared to traditional OOP languages.

In traditional OOP:
- When defining a **class**, we are really defining a single **type** (i.e. a set of functions).
- An **instance** of a class, represents a function belonging to that class.

In Haskell:
- A **class** represents a set of type constructors.
- An **instance** of that class, represents a type constructor belonging to that class.

For example:
```haskell
class Functor f where
  fmap :: (a -> b) -> f a -> f b
```

Mathematically speaking, we are defining a new set of type constructors, and we'll call this set `Functor`. In other words, this `class` declaration is roughly equivalent to:

$$
Functor := \lbrace f \mid f \in (* \rightarrow *) \text{ and } P(f) \rbrace
$$

$$
where \space P(f) = f \text{ has some function definition for } fmap
$$

We can infer the kind `* -> *`, by looking at the type signature for `fmap`.

We can also clearly see that $Functor \subset (* \rightarrow *)$

Also, note that `fmap` is a function which can be overloaded.

Another example:
```haskell
class Eq a where
  (==) :: a -> a -> Bool
```

In this case, `a` has kind `*`. Thus, $Eq \subset *$

In the special case where our class is a subset of `*`, we call this a **type class.**

Otherwise, all other classes are called **constructor classes**.

### Instances
Now let's look at some **instances**.

#### Example 1
```haskell
instance Functor Maybe where
  fmap _ Nothing  = Nothing
  fmap g (Just a) = Just $ g a
```

Note that `Maybe` is an existing type constructor of kind `* -> *`.

This `instance` syntax is asserting that the `Maybe` type constructor belongs to the `Functor` class/set, by defining an `fmap` implementation.

#### Example 2
```haskell
instance Functor (Either a) where
  fmap _ (Left x)  = Left x
  fmap f (Right y) = Right (f y) 
```

Here, we know that `Either` has kind `* -> * -> *`. Thus, `Either a` has kind `* -> *`.

Thus, `Either a` is an appropriate candidate to define an `fmap` function for, and thus belong to the `Functor` class/set.

#### Function Overloads
Note here, that we've acheived function overloading for `fmap` - the function has different implementations for `Maybe` and `Either a`.

### Type Constructor Constraints
As noted above, one of the principal benefits of ad-hoc polymorphism is that, it allows us to define **constraints** on type constructor variables.

Let's look at some examples.

#### Example: Function type signatures
Consider:
```haskell
foo :: f a -> a -> String
```

Here, `foo` works $\forall f \in (* \rightarrow *)$, and $\forall a \in (*)$.

However, suppose we add some type constraints:
```haskell
foo :: (Functor f, Num a, Show a) => f a -> a -> String
```

Now, we've restricted the possible values for `f` and `a`.

Now, `foo` works 
$$
\forall f \in Functor \subset (* \rightarrow *)
$$
$$
\forall a \in Num \cap Show \subset (* \rightarrow *)
$$

#### Example: Instance declarations
Consider:
```haskell
instance Foo [a] where
```

This means that, $\forall a \in (*)$, $[a] \in Foo$.

However, adding type constraints:
```haskell
instance (Eq a) => Foo [a] where
```

This means that, $\forall a \in Eq \subset (*)$, $[a] \in Foo$

#### Example: Class declarations
Consider this class declaration, which lacks constraints:
```haskell
class Applicative f where
  pure :: a -> f a
  (<*>) :: f (a -> b) -> f a -> f b
```

This essentially means:

$$
Applicative := \lbrace f \mid f \in (* \rightarrow *) \text{ and } P(f) \rbrace
$$

$$
where \space P(f) = f \text{ has some function definition for both } pure \text{ and <*>}
$$

Now consider this class declaration, which includes a constraint:
```haskell
class (Functor f) => Applicative f where
  pure :: a -> f a
  (<*>) :: f (a -> b) -> f a -> f b
```

The constraint changes the the definition to:

$$
Applicative := \lbrace f \mid f \in Functor \subset (* \rightarrow *) \text{ and } P(f) \rbrace
$$

$$
where \space P(f) = f \text{ has some function definition for both } pure \text{ and <*>}
$$

In other words, $Applicative \subset Functor \subset (* \rightarrow *)$

This type constraint allows us to define "class subsets".

## Comparison With OOP
In OOP, parametric polymorphism (i.e. unconstrained type constructor variables) can be achieved like:
```typescript
type MyObj<T, U> = {
  prop1: T,
  prop2: U,
}
```

where `T` and `U` are the variables.

Ad-hoc polymorphism can be achieved by adding additional constraints on the type variables, i.e.
```typescript
type MyObj<T extends Foo, U extends Bar> = {
  prop1: T,
  prop2: U,
}
```
