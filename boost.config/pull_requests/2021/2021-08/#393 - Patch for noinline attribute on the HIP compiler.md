# #393 Patch for noinline attribute on the HIP compiler. [Merged]

> Username: jzmaddock  
> Created at: 2021-08-08 10:54:43 UTC  
> Updated at: 2022-11-20 17:45:38 UTC  
> Merged at: 2021-08-09 18:07:38 UTC  
> Closed at: 2021-08-09 18:07:38 UTC  
> Url: https://github.com/boostorg/config/pull/393  

Fixes https://github.com/boostorg/config/issues/392.

---

## Review 1 [Commented]

> Username: atamazov  
> Created_at: 2022-04-26 14:47:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/393#pullrequestreview-953518928  

I recommend re-implementing the fix.

📁 include/boost/config/detail/suffix.hpp

```diff
 630 |        // see: https://svn.boost.org/trac/boost/ticket/9392
 631 | #      define BOOST_NOINLINE __attribute__ ((noinline))
 632 |+ #    elif defined(HIP_VERSION)
```

> Username: atamazov  
> Created_at: 2022-04-26 14:47:08 UTC  
> Updated_at: 2022-04-26 14:47:27 UTC  
> Url: https://github.com/boostorg/config/pull/393#discussion_r858808126  

Please see https://github.com/boostorg/config/issues/392#issuecomment-1109889533


---
