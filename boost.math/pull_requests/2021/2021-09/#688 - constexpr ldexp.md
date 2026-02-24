# #688 constexpr ldexp [Merged]

> Username: mborland  
> Created at: 2021-09-06 18:50:13 UTC  
> Updated at: 2021-09-12 16:56:42 UTC  
> Merged at: 2021-09-12 15:40:13 UTC  
> Closed at: 2021-09-12 15:40:13 UTC  
> Url: https://github.com/boostorg/math/pull/688  

As requested by @ckormanyos

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-09-06 18:53:37 UTC  
> Url: https://github.com/boostorg/math/pull/688#issuecomment-913811261  

> As requested by @ckormanyos  
  
Thank you @mborland. This is very useful. It is amazing, how quickly and reliably you're implementing, testing and documenting these functions

---

## Comment 2

> Username: mborland  
> Created_at: 2021-09-08 20:16:44 UTC  
> Url: https://github.com/boostorg/math/pull/688#issuecomment-915539767  

@NAThompson and @jzmaddock Pending review this should be good. The two CI failures are network errors.

---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2021-09-09 02:16:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/688#pullrequestreview-749765923  

📁 include/boost/math/ccmath/ldexp.hpp

```diff
  40 |+         --abs_exp;
  41 |+     }
  42 |+ 
```

> Username: NAThompson  
> Created_at: 2021-09-09 02:16:39 UTC  
> Url: https://github.com/boostorg/math/pull/688#discussion_r704899066  

This whole loop is `constexpr`!?


---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2021-09-09 10:32:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/688#pullrequestreview-750164831  

📁 include/boost/math/ccmath/ldexp.hpp

```diff
  89 |+                boost::math::ccmath::isnan(arg) ? arg :
  90 |+                exp == 0 ? arg :
  91 |+                arg * boost::math::ccmath::detail::intexp2<Real>(exp);
```

> Username: jzmaddock  
> Created_at: 2021-09-09 10:32:08 UTC  
> Url: https://github.com/boostorg/math/pull/688#discussion_r705202038  

I think there's an issue with this approach: lets say arg is very large and exp is large and negative, in that situation the result of intexp2 can go to zero even though the result is expected to be finite.  
  
I would simplify this and do something more like:  
  
```  
while(exp > 0)  
{  
   arg *=2;  
   --exp;  
}  
while(exp < 0)  
{  
   arg /= 2;  
   ++exp;  
}  
```  
  
Which should return 0 or INF in the edge cases anyway?


---

## Comment 5

> Username: mborland  
> Created_at: 2021-09-12 12:37:23 UTC  
> Url: https://github.com/boostorg/math/pull/688#issuecomment-917628234  

This is clean on CI with the updated implementation.

---
