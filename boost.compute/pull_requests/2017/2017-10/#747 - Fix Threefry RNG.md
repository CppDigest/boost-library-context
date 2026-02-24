# #747 Fix Threefry RNG [Merged]

> Username: jszuppe  
> Created at: 2017-10-12 20:16:13 UTC  
> Updated at: 2017-10-14 09:11:55 UTC  
> Merged at: 2017-10-14 09:11:55 UTC  
> Closed at: 2017-10-14 09:11:55 UTC  
> Url: https://github.com/boostorg/compute/pull/747  

Fixes #745

---

## Comment 1

> Username: jszuppe  
> Created_at: 2017-10-12 20:18:11 UTC  
> Url: https://github.com/boostorg/compute/pull/747#issuecomment-336253768  

@Ulfgard Please review

---

## Comment 2

> Username: jszuppe  
> Created_at: 2017-10-12 21:57:17 UTC  
> Url: https://github.com/boostorg/compute/pull/747#issuecomment-336285800  

I'll focus on fixing Travis CI builds tomorrow.

---

## Review 3 [Commented]

> Username: Ulfgard  
> Created_at: 2017-10-13 07:26:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compute/pull/747#pullrequestreview-69143752  

📁 include/boost/compute/random/threefry_engine.hpp

```diff
 127 |+ 
 128 |+         m_counter[0] += lo;
 129 |+         m_counter[1] += hi + (m_counter[0] < lo ? 1 : 0);
```

> Username: Ulfgard  
> Created_at: 2017-10-13 07:26:00 UTC  
> Updated_at: 2017-10-13 09:39:43 UTC  
> Url: https://github.com/boostorg/compute/pull/747#discussion_r144481841  

consider making counter a 64bit integer just for making this logic simpler. This also allows to make counter an atomic for thread safety.

> Username: jszuppe  
> Created_at: 2017-10-13 08:03:17 UTC  
> Updated_at: 2017-10-13 09:39:43 UTC  
> Url: https://github.com/boostorg/compute/pull/747#discussion_r144488486  

I can make it `ulong_` (and the `m_key` too), but I'm not sure about making the counter an atomic value. Other Boost.Compute RNGs do not promise thread safety (just like RNGs in C++ Standard Library).


---

## Comment 4

> Username: coveralls  
> Created_at: 2017-10-13 11:03:38 UTC  
> Url: https://github.com/boostorg/compute/pull/747#issuecomment-336421330  

[![Coverage Status](https://coveralls.io/builds/13702184/badge)](https://coveralls.io/builds/13702184)  
  
Coverage increased (+0.03%) to 83.766% when pulling **77b4a4910b80f9d665def4d9c8e494fd67a27458 on jszuppe:fix_threefry** into **d36ef02b7860b9bcf5ea4911b77f1aa77b5f4059 on boostorg:develop**.

---

## Comment 5

> Username: Ulfgard  
> Created_at: 2017-10-14 08:01:46 UTC  
> Url: https://github.com/boostorg/compute/pull/747#issuecomment-336618447  

lloks good. ship it :)

---
