# #1391 feat: make side strategy configurable [Merged]

> Username: barendgehrels  
> Created at: 2025-04-19 10:53:33 UTC  
> Updated at: 2025-07-16 15:53:14 UTC  
> Merged at: 2025-04-25 06:34:42 UTC  
> Closed at: 2025-04-25 06:34:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1391  

As discussed in #1369

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2025-04-19 10:54:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1391#pullrequestreview-2779937775  

📁 include/boost/geometry/core/config.hpp

```diff
  27 |+ #if    ! defined(BOOST_GEOMETRY_DEFAULT_STRATEGY_SIDE_USE_SIDE_BY_TRIANGLE) \
  28 |+     && ! defined(BOOST_GEOMETRY_DEFAULT_STRATEGY_SIDE_USE_SIDE_ROBUST)
  29 |+ #define BOOST_GEOMETRY_DEFAULT_STRATEGY_SIDE_USE_SIDE_BY_TRIANGLE
```

> Username: barendgehrels  
> Created_at: 2025-04-19 10:54:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1391#discussion_r2051452379  

It cannot be `robust` yet, it gives unit test errors.  
They can better be solved after merging #1369


---

## Review 2 [Approved]

> Username: tinko92  
> Created_at: 2025-04-19 15:32:51 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1391#pullrequestreview-2780005572  

Looks good to me!

📁 test/geometry_test_common.hpp

```diff
 217 |+     <
 218 |+         bg::cartesian_tag
 219 |+     >::type;
```

> Username: tinko92  
> Created_at: 2025-04-19 15:32:07 UTC  
> Updated_at: 2025-04-19 15:32:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1391#discussion_r2051508961  

(Very minor) I think L217-L219 would usually be one indentation level deeper?

> Username: barendgehrels  
> Created_at: 2025-04-19 17:34:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1391#discussion_r2051538780  

That's right, thanks.  
✅


---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2025-04-24 11:50:51 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1391#pullrequestreview-2790805462  

I am OK with this, thanks!

---
