# #107 - [Foldable] Foldable unpack versus fold_transform [Closed]

> Username: viboes  
> Created at: 2015-06-13 14:27:35 UTC  
> Updated at: 2015-06-14 14:22:47 UTC  
> Closed at: 2015-06-13 17:23:38 UTC  
> Url: https://github.com/boostorg/hana/issues/107  

Hana uses transform instead of map. I don't find intuitive at all the name `unpack`. What about `fold_transform`?

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-06-13 15:40:14 UTC  
> Url: https://github.com/boostorg/hana/issues/107#issuecomment-111722178  

I find the name `unpack` very intuitive, and `fold_transform` makes no sense, since its doing neither a transform nor a fold. Also this is one of the areas where Hana might inherit from Fit. Fit has `unpack` as well.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-06-13 17:23:38 UTC  
> Url: https://github.com/boostorg/hana/issues/107#issuecomment-111734654  

> Hana uses transform instead of map.  
  
Yes, for 1. the resemblance with `std::transform` and 2. the name conflict with Hana's `Map` data structure.  
  
> I don't find intuitive at all the name unpack. What about fold_transform?  
  
I agree with @pfultz2 here. `unpack` is specified as follows:  
  
``` c++  
unpack([x1, ..., xn], f) == f(x1, ..., xn)  
```  
  
This bears little to no resemblance to a fold or a transform, and I think it is very intuitive to think of it as `unpack`ing a sequence into a function.

---

## Comment 3

> Username: viboes  
> Created at: 2015-06-14 12:33:32 UTC  
> Url: https://github.com/boostorg/hana/issues/107#issuecomment-111822399  

Sorry, I misunderstood it. The standard uses `std::apply`for this function when `[]` is a `std::tuple`.

---

## Comment 4

> Username: ldionne  
> Created at: 2015-06-14 14:22:47 UTC  
> Url: https://github.com/boostorg/hana/issues/107#issuecomment-111830723  

Yes, this is `std::apply` with the arguments reversed. The rationale for the order of arguments and the name is available [here](http://ldionne.com/hana/structboost_1_1hana_1_1Foldable.html#a0e3e724580e826eaa39a934eefa2b328).
