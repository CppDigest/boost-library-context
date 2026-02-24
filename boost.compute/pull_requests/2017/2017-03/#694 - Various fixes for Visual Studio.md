# #694 Various fixes for Visual Studio  [Merged]

> Username: jszuppe  
> Created at: 2017-03-18 14:58:24 UTC  
> Updated at: 2017-03-21 03:11:32 UTC  
> Merged at: 2017-03-21 03:11:12 UTC  
> Closed at: 2017-03-21 03:11:13 UTC  
> Url: https://github.com/boostorg/compute/pull/694  

Fixes https://github.com/boostorg/compute/issues/673.

---

## Comment 1

> Username: coveralls  
> Created_at: 2017-03-18 16:12:06 UTC  
> Url: https://github.com/boostorg/compute/pull/694#issuecomment-287556071  

[![Coverage Status](https://coveralls.io/builds/10661564/badge)](https://coveralls.io/builds/10661564)  
  
Coverage remained the same at 83.351% when pulling **2d604e765675de37004d1b22d9440736e8f8f087 on haahh:pr_vs_fixes** into **cc30762666aa9b20f62751cdf119f2cf1a103532 on boostorg:develop**.

---

## Review 2 [Commented]

> Username: kylelutz  
> Created_at: 2017-03-21 03:10:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compute/pull/694#pullrequestreview-28014682  

📁 include/boost/compute/detail/parameter_cache.hpp

```diff
 127 |+         DETAIL_SNPRINTF(buf, sizeof(buf), "%d.%d.%d", BOOST_COMPUTE_VERSION_MAJOR,
 128 |+                                                       BOOST_COMPUTE_VERSION_MINOR,
 129 |+                                                       BOOST_COMPUTE_VERSION_PATCH);
```

> Username: kylelutz  
> Created_at: 2017-03-21 03:10:00 UTC  
> Url: https://github.com/boostorg/compute/pull/694#discussion_r107064784  

I wonder if there's any utilities somewhere in Boost for doing this platform-specific stuff..


---

## Comment 3

> Username: kylelutz  
> Created_at: 2017-03-21 03:11:32 UTC  
> Url: https://github.com/boostorg/compute/pull/694#issuecomment-287964316  

Merged! Thanks for the fixes!

---
