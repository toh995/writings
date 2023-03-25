# Monad Intuition

Lately while studying [haskell](https://www.haskell.org/), I've been wrestling with the idea of a
[`Monad`](https://hackage.haskell.org/package/base-4.18.0.0/docs/Control-Monad.html#t:Monad).
Although I understand the formal definition, I haven't fully grokked the concept yet.

This document represents my current intuition on the subject, and is also a personal exercise to organize my current
thoughts. It's NOT meant to be a final, definitive document, as I expect my intuititon to evolve over time. Nor is this
meant to be a definitive `Monad` tutorial - I'd like to avoid the
[monad tutorial fallacy](https://byorgey.wordpress.com/2009/01/12/abstraction-intuition-and-the-monad-tutorial-fallacy/).

Now let's dig in.

## Functors and Applicative Functors

If we look at the formal definition for `Monad`, we'll see:
```haskell
class Applicative m => Monad m where
```

This means that every `Monad` must also be an
[`Applicative Functor`](https://hackage.haskell.org/package/base-4.18.0.0/docs/Control-Applicative.html#t:Applicative).

But if we look at the definition for `Applicative Functor`, we'll see that:
```haskell
class Functor f => Applicative f where
```

So every `Monad` is also a [`Functor`](https://hackage.haskell.org/package/base-4.18.0.0/docs/Data-Functor.html#t:Functor).

## Functors as Containers

Let's start by exploring `Functors`. Any intuition gained on `Functors` can also be applied to `Monads`
(since all `Monads` are `Functors`).

When first learning about `Functors`, I had this intuition:

> A `Functor` represents a container of values that we can map over.

We'll see later, that this intuition can fail in some cases. But for now, let's develop the intuition further.

If we look at the definition of `Functor`:
```haskell
class Functor f where
  fmap :: (a -> b) -> f a -> f b
```

We'll see that the `Functor` typeclass requires an `fmap` function to be implemented.

Roughly speaking, the `fmap` function takes two arguments: a function, and a container of values. With those two arguments,
the `fmap` function constructs a new container, by applying the function to each of the old container's values.

For example:
```haskell
fmap (+1) [1,2,3] -- evaluates to [2,3,4]
```

In this example, our "container" is a "List". Note that, we get a new list `[2,3,4]`, constructed by applying `(+1)`
to each of the original list's values.

Some other examples:
```haskell
fmap show [10,8,20] -- evaluates to ["10", "8", "20"]
fmap (++ [3,4]) (Just [1,2]) -- evaluates to `Just [1,2,3,4]`
fmap sum (Just [5,6,1]) -- evaluates to `Just 12`
```

Note that we can have different types of containers - in our examples, we have `List` and `Maybe`. In addition, we can
easily extend this idea to many other containers/data structures, e.g. sets, graphs, trees, etc.

## Applicative Functors

## Summary and Next Steps
I wonder if monad transformers. At first glance, it seems like a way to combine two monads.

