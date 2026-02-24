# #110 - Confussion between the concrete type and the type constructor [Closed]

> Username: viboes  
> Created at: 2015-06-13 14:52:41 UTC  
> Updated at: 2015-06-21 20:11:13 UTC  
> Closed at: 2015-06-21 20:11:13 UTC  
> Url: https://github.com/boostorg/hana/issues/110  

Sometimes we consider the data type Tag as been the model of the Concept: e. g. `Tuple` is declared as a concrete type of a `Functor`  and as a concrete type of a `Foldable`.   
  
However `Functor` has instances `F` such that  
  
"  
Given `F` a `Functor`, the signature is `transform:F(T)×(T→U)→F(U)`  
"  
  
and `Foldable` has instances F(T) such  
"  
Given a Foldable xs of data type F(T), a function f:S×T→S and an initial state s of data type S, the signatures for fold.left are  
  
fold.left:F(T)×S×(S×T→S)→S  
"  
  
How Tuple can be a model of Foldable?

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-13 21:20:52 UTC  
> Url: https://github.com/boostorg/hana/issues/110#issuecomment-111753654  

IIUC, you are asking why `Tuple` is both defined as a `F(T)` and a `F`, in different concepts, right? That's just an abuse of notation. A `Foldable` is a `F` too.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-06-13 21:27:28 UTC  
> Url: https://github.com/boostorg/hana/issues/110#issuecomment-111753861  

Ok, so technically, what should be written is:  
  
Given a tag `F` which is a `Foldable`, an object `xs` of tag `F` and a function `f` of type `S x T -> S`, the signature of `fold.left` is   
  
```  
    fold.left : F(T) x S x (S x T -> S) -> S  
```  
  
However, I think the way it is currently written is also perfectly comprehensible.

---

## Comment 3

> Username: viboes  
> Created at: 2015-06-14 11:41:33 UTC  
> Url: https://github.com/boostorg/hana/issues/110#issuecomment-111815430  

I agree. A `Foldable is a type constructor, and as such must be defined. What about  
  
Given `F` a `Foldable`, the signature of `fold.left` is   
  
```  
    fold.left : F(T) x S x (S x T -> S) -> S  
```

---

## Comment 4

> Username: ldionne  
> Created at: 2015-06-14 13:21:04 UTC  
> Url: https://github.com/boostorg/hana/issues/110#issuecomment-111826901  

Works for me.
