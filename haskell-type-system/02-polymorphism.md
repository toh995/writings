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

## Parametric Polymorphism
Parametric polymorphism refers to the usage of _unconstrained_ type constructor variables.

For example:
```haskell
id :: a -> a
```

In this case, `a` can refer to any arbitrary type, without restriction.

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

Mathematically speaking, we are defining a new set of type constructors, and we'll call this set `Functor`. In other words, this `class` declaration is
roughly equivalent to:

$$
Functor := \{ f \mid f \in (* \rightarrow *) \text{ and } P(f) \}
\\
where \space P(f) = f \text{ has some function definition for } fmap
$$

We can infer the kind `* -> *`, by looking at the type signature for `fmap`.

We can also clearly see that $Functor \subset (* -> *)$

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
As noted above, one of the principal benefits of ad-hoc polymorphism is that, it allows us to define constraints on type constructors.

#### Example: Function type signatures
Here's an example type signature with NO constraints:

```haskell
sum :: t a -> a
```
Here, we have two type constructors: `a` and `t`.

Observe that `a` has kind `*`, and `t` has kind `* -> *`.

In the absence of any constraints, we expect the `sum` function to be valid for any type constructors `a` and `t`, such that
$$
a \in (*)
\\
t \in (* \rightarrow *)
$$

Now, consider this type signature which has constraints:
```haskell
sum :: (Num a, Foldable t) => t a -> a
```

Now, the `sum` function will only be valid for the type constructors `a` and `t`, such that
$$
a \in Num \subset (*)
\\
t \in Foldable \subset (* \rightarrow *)
$$

Another example:
```haskell
foo :: (Num a, Show a, Show b) => a -> a -> b -> String
```

Here, `foo` is valid for all type constructors `a` and `b`, such that
$$
a \in Num \subset (*)
\\
a \in Show \subset (*)
\\
b \in Show \subset (*)
$$


#### Example: Instance declarations
```haskell
instance (Eq a) => Eq [a] where
  []     == []     = True
  (x:xs) == (y:ys) = x == y && xs == ys
  _      == _    = False
```

This is basically saying, $\forall a \in (*), \text{ if } a \in Eq, \text{ then } [a] \in Eq$.

#### Example: Class declarations
Consider this class declaration, which lacks constraints:
```haskell
class Applicative f where
  pure :: a -> f a
  (<*>) :: f (a -> b) -> f a -> f b
```

This essentially means:
$$
Applicative := \{ f \mid f \in (* \rightarrow *) \text{ and } P(f) \}
\\
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
Applicative := \{ f \mid f \in Functor \subset (* \rightarrow *) \text{ and } P(f) \}
\\
where \space P(f) = f \text{ has some function definition for both } pure \text{ and <*>}
$$

In other words, $Applicative \subset Functor \subset (* \rightarrow *)$
