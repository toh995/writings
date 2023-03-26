# Haskell Monad Intuition Notes

Here are my various intuitions about monads in haskell so far (subject to change/evolve over time).

This document is a personal exercise to organize my current thinking around monads.
This is NOT a definitive monad tutorial - I'd like to avoid the 
[monad tutorial fallacy](https://byorgey.wordpress.com/2009/01/12/abstraction-intuition-and-the-monad-tutorial-fallacy/).

## What IS a monad?
So far, I have the following intuitions for monads:
- Monad as a container of values
- Monad as a context for a value
- Monad as an instruction-set

### Monad as a container of values
Concrete examples: lists, sets, trees, graphs, etc.

- In this category, we might have traditional "container" data structures
- `Functor`, `Applicative`, and `Monad` give us various tools for applying functions to manipulate the contents of the container

### Monad as a context for a value
Concrete examples: `IO`, `Maybe`

- The idea is that, we can wrap a "pure" value inside of an "impure" context (i.e. `IO`)
- Monads give us a clear separation between "pure" and "impure" code.

In this paradigm:
- (Functor) `<$>` apply a pure function to the value inside the context
- (Applicative) `pure` inject a pure value into the context
- (Applicative) `<*>` after injecting a pure function into the context, apply that function to other "context wrappers"
- (Monad) `>>=` take the pure value inside of the context, and produce another monad

### Monad as an instruction-set
Concrete examples: `State`, `Parser`, `(-> r)`, `IO`

- In this paradigm, the monad represents a set of instructions to execute later.
- The definition of monad does NOT necessitate a mechanism to EXECUTE the instruction-set. This is usually defined OUTSIDE of the monad (e.g. for `State`, we have `runState`, which is a custom function unique to `State`).
- Monad manipulation, then, becomes a way to combine existing instruction-set(s) to create new ones
- As a corollary: just because you operate on a monad, does NOT necessarily mean that the instruction-set has been executed. Again, there is a separate mechanism to EXECUTE the instruction-set.

In this paradigm:
- (Functor) `<$>` create a new instruction-set. This new instruction-set will execute the first instruction-set, then apply a pure function to the results
- (Applicative) `pure` create a new instruction-set, which, when executed, will return the provided value
- (Applicative) `<*>` combine two instruction-sets into a new one. The new instruction-set will execute the first instruction-set, then execute the second one.  The final result of the new instruction-set, will be the ouptut of the first instruction-set (which is a function), applied to the output of the second instruction-set.
- (Monad) `>>=` create a new instruction-set, which will execute the first instruction-set, then thread its results to the provided function, which will produce a second instruction-set. After this, execute this second instruction-set. The output of this second instruction-set is the final output.

## Why are monads useful?

### Clear separation between pure and impure code
Self-explanatory. An obvious example is the `IO` monad. We can write pure code to operate on the results of an `IO` action.

### Abstract away tedious bookkeeping
Fundamentally, monads (and functors/applicative functors) allow us to abstract away tedious bookkeeping that we normally have to do.

The idea is that we write all of the tedious bookkeeping exactly ONCE, inside of the instance declarations for `Monad`/`Applicative`/`Functor`.
Once we've done that, we no longer have to think about bookkeeping in normal everyday code - instead, we can just use the simpler built-in functions like
`<$>`, `<*>`, `>>=`, `pure`, etc, and we can be confident that the bookkeeping will be taken care of for us.

Examples where this is useful: `Maybe`, `State`, `Parser`, etc.

#### Example: `Maybe`
Consider the following function that deals with `Maybe` values:
```haskell
sumMaybes :: Maybe Int -> Maybe Int -> Maybe Int
sumMaybes Nothing   _       = Nothing
sumMaybes _         Nothing = Nothing
sumMaybes (Just n) (Just m) = Just (n + m)
```

Here, the extra "bookkeeping" is the pattern matching on `Nothing` and `Just`.

We can rewrite this, abstracting away the bookkeeping, using an `Applicative`:
```haskell
sumMaybes :: Maybe Int -> Maybe Int -> Maybe Int
sumMaybes = liftA2 (+)
```

The bookkeeping code is housed in the `Applicative` instance declaration:
```haskell
instance Applicative Maybe where
  pure = Just

  Just f  <*> m = f <$> m
  Nothing <*> _ = Nothing
```
