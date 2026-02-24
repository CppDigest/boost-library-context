# #568 Remove boost.lambda dependency [Merged]

> Username: mborland  
> Created at: 2021-03-14 07:00:50 UTC  
> Updated at: 2021-03-17 18:31:22 UTC  
> Merged at: 2021-03-15 18:32:43 UTC  
> Closed at: 2021-03-15 18:32:43 UTC  
> Url: https://github.com/boostorg/math/pull/568  



---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2021-03-14 12:58:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/568#pullrequestreview-611693272  

📁 include/boost/math/tools/polynomial.hpp

```diff
 327 |       }
 328 |    }
 329 | #ifdef BOOST_MATH_HAS_IS_CONST_ITERABLE
```

> Username: jzmaddock  
> Created_at: 2021-03-14 12:58:16 UTC  
> Url: https://github.com/boostorg/math/pull/568#discussion_r593896639  

I suspect BOOST_MATH_HAS_IS_CONST_ITERABLE is always set in C++11 land?  
  
Other than that, this all looks good to me,, thanks!

> Username: mborland  
> Created_at: 2021-03-14 13:08:41 UTC  
> Updated_at: 2021-03-14 13:08:42 UTC  
> Url: https://github.com/boostorg/math/pull/568#discussion_r593898095  

BOOST_MATH_HAS_IS_CONST_ITERABLE requires cxx14_variable_templates so it has to stay put for now.

> Username: jzmaddock  
> Created_at: 2021-03-14 14:09:15 UTC  
> Updated_at: 2021-03-14 14:09:16 UTC  
> Url: https://github.com/boostorg/math/pull/568#discussion_r593906671  

Ah, shame.  No matter.


---

## Comment 2

> Username: mborland  
> Created_at: 2021-03-15 18:22:40 UTC  
> Url: https://github.com/boostorg/math/pull/568#issuecomment-799645414  

This one should also be good to go. CI is clean.

---
