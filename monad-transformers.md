---
date: 2023-11-27
---
- Find out about standard monads...?


We can define "families" of monads. i.e.

```haskell
class Monad m => StateMonad m s where
class Monad m => ErrorMonad m where
class Monad m => WriterMonad m where
class Monad m => ReaderMonad m r where
```

We could theoretically have some monad $m \in StateMonad \cap ErrorMonad \cap WriterMonad \cap ReaderMonad \subset Monad$.

To build these, we can use **monad transformers**.

Intuitively, a monad transformer takes an existing monad, and wraps it in another existing monad, to create a new monad.

i.e. it creates *nested monads*.

We can define monad transformers like the following:
```haskell
class MonadT t where
    lift :: Monad m => m a -> t m a
```



The order that we apply transformations, matters - switching the order can change the behavior.
