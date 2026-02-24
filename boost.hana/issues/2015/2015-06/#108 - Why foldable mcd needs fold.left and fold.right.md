# #108 - [Foldable] Why foldable mcd needs fold.left and fold.right? [Closed]

> Username: viboes  
> Created at: 2015-06-13 14:29:47 UTC  
> Updated at: 2015-06-14 14:12:38 UTC  
> Closed at: 2015-06-13 21:04:36 UTC  
> Url: https://github.com/boostorg/hana/issues/108  

Haskell `Foldable` type class has a `foldr` mcd. Why Hana needs `fold.left` and `fold.right`?

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-13 21:04:36 UTC  
> Url: https://github.com/boostorg/hana/issues/108#issuecomment-111752540  

If I remember correctly, it is because the default definition in terms of `foldr` was just way too inefficient. If you look at the definition from Haskell:  
  
``` haskell  
foldl :: (b -> a -> b) -> b -> t a -> b  
foldl f z t = appEndo (getDual (foldMap (Dual . Endo . flip f) t)) z  
```  
  
This uses `foldMap`, which is actually a `foldr`, to build a huge chain of function compositions, and then it throws the initial state (`z`) in there. With Hana, that would mean creating a huge chain of `compose`d function objects, and then calling that, which won't be efficient.

---

## Comment 2

> Username: viboes  
> Created at: 2015-06-14 11:29:05 UTC  
> Url: https://github.com/boostorg/hana/issues/108#issuecomment-111814966  

Couldn't fold_left be defined by default by some mcd?  
  
Could the rationale be added to the documentation if not already there?

---

## Comment 3

> Username: ldionne  
> Created at: 2015-06-14 14:12:38 UTC  
> Url: https://github.com/boostorg/hana/issues/108#issuecomment-111829685  

I'll define `fold_right` in terms of `unpack`, which is itself defined in terms of `fold_left`.  
  
I'll also add a note that the default implementation of `unpack` in terms of `fold_left` is pretty inefficient, so people should ideally define `unpack` if they want speed.
