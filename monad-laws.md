---
date: 2023-11-23
---
# Monad Laws
## Functor
1. **Identity**
```haskell
fmap id == id
```
2. **Composition**
```haskell
fmap (f . g) == fmap f . fmap g
```

## Applicative
1. **Identity**
```haskell
pure id <*> v = v
```
2. **Composition**
```haskell
pure (.) <*> u <*> v <*> w = u <*> (v <*> w)
```
3. **Homomorphism**
```haskell
pure f <*> pure x = pure (f x)
```
4. **Interchange**
```haskell
u <*> pure y = pure ($ y) <*> u
```

## Monad
1. **Left identity**
```haskell
return a >>= k = k a
```
2. **Right identity**
```haskell
m >>= return = m
```
3. **Associativity**
```haskell
m >>= (\x -> k x >>= h) = (m >>= k) >>= h
```
