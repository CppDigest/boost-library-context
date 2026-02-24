# #105 - [Design][Documentation] Concepts do not convert from one to the other [Closed]

> Username: viboes  
> Created at: 2015-06-13 14:15:30 UTC  
> Updated at: 2015-06-15 19:37:00 UTC  
> Closed at: 2015-06-15 19:37:00 UTC  
> Url: https://github.com/boostorg/hana/issues/105  

In http://ldionne.com/hana/structboost_1_1hana_1_1Foldable.html it is written that   
  
```  
any Foldable can be converted to a Sequence  
```  
  
Do you mean that any instance/model  of Foldable can be seen as an instance+model of a Sequence?   
  
The function `to<Tuple>` e.g. is a function from concrete models to a concrete model.  
Do we really need to make this conversion to be able to see a Foldable as a Sequence?  
  
I suggest to reword this kind of descriptions.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-13 20:51:14 UTC  
> Url: https://github.com/boostorg/hana/issues/105#issuecomment-111751897  

> In http://ldionne.com/hana/structboost_1_1hana_1_1Foldable.html it is written that  
>   
> any Foldable can be converted to a Sequence  
> Do you mean that any instance/model of Foldable can be seen as an instance+model of a Sequence?  
  
No. What I mean is precisely that any object which is a model of the `Foldable` concept can be converted to a new object which is a model of the `Sequence` concept. Like it is written in the documentation, for a `Foldable` object `xs` with a linearization of `[x1, ..., xn]` and a `Sequence` tag `S`,   
  
``` c++  
to<S>(xs) == make<S>(x1, ..., xn)  
```  
  
> The function to<Tuple> e.g. is a function from concrete models to a concrete model.  
  
I don't understand what you mean. `to<Tuple>` is a function from any `Foldable` (the concept) to a `Tuple` (the concrete model).  
  
> Do we really need to make this conversion to be able to see a Foldable as a Sequence?  
  
Yes, since `Sequence` is a more refined concept than `Foldable`. For example, it makes sense to compute all the permutations of a `Tuple`, but it does not make sense to compute the permutations of a `Range`, since the result can't be expressed as a `Range`. What you can do, however, is perform a lossy conversion from `Range` to `Tuple`, and then use this as a `Sequence`, which it is indeed.  
  
Saying that we could see any `Foldable` as a `Sequence` without explicit conversion is like saying that a `std::optional` is a boolean (so we should define `&&` and `||`), simply because it can be converted to a `bool`. The right way is to perform a lossy conversion to `bool` first, and then do whatever you want.  
  
> I suggest to reword this kind of descriptions.  
  
I reworded this description, although I honestly found it pretty clear. If you have other examples of descriptions you find unclear, please let me know.
