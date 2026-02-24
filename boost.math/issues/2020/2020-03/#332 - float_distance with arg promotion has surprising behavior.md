# #332 - float_distance with arg promotion has surprising behavior [Closed]

> Username: NAThompson  
> Created at: 2020-03-28 16:04:38 UTC  
> Updated at: 2020-04-10 10:52:43 UTC  
> Closed at: 2020-04-10 10:52:43 UTC  
> Url: https://github.com/boostorg/math/issues/332  

Allowing two different template arguments in float_distance gives very surprising behavior, since it's not clear which floating point type the user intends to calculate the distance between.  
  
If one type is integral, then this is sensible: Promote it to the floating point type. But if both are floating what is the intended behavior? The actual behavior is that it computes the more precise floating point distance.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-03-28 17:53:36 UTC  
> Url: https://github.com/boostorg/math/issues/332#issuecomment-605496003  

I'm unsure about this one, BTW for integral arguments things are potentially even more confusing because integer types are treated as `double`, so that `float_distance(0, float_value)` actually computes at double precision.  
  
I'm tempted to suggest that we convert the mixed-arg version to static_assert internally with a reasonable helpful message and then see what breaks.  However, it is a breaking change, which would be too late for the next release I think (I'm hoping we might have time for one more merge of develop->release to include the wavelet code).

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-03-28 19:31:01 UTC  
> Updated at: 2020-03-28 20:51:17 UTC  
> Url: https://github.com/boostorg/math/issues/332#issuecomment-605508013  

I'll make a PR for the breaking change and we can merge it at our convenience.
