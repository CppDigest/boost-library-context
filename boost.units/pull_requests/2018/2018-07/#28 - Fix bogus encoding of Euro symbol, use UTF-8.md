# #28 Fix bogus encoding of Euro symbol, use UTF-8 [Merged]

> Username: jwakely  
> Created at: 2018-07-06 09:35:24 UTC  
> Updated at: 2021-01-18 15:08:55 UTC  
> Merged at: 2018-09-14 07:50:17 UTC  
> Closed at: 2018-09-14 07:50:17 UTC  
> Url: https://github.com/boostorg/units/pull/28  

Fixed this ancient issue: https://svn.boost.org/trac10/ticket/6150

---

## Review 1 [Commented]

> Username: jwakely  
> Created_at: 2018-07-06 10:05:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/units/pull/28#pullrequestreview-134958142  

📁 example/autoprefixes.cpp

```diff
  69 |   static constexpr const char* name() { return("EUR"); }
  70 |-   static constexpr const char* symbol() { return("�"); }
  70 |+   static constexpr const char* symbol() { return("€"); }
```

> Username: jwakely  
> Created_at: 2018-07-06 10:05:19 UTC  
> Url: https://github.com/boostorg/units/pull/28#discussion_r200608721  

The gihub UI shows these as the same, but if you look at https://patch-diff.githubusercontent.com/raw/boostorg/units/pull/28.diff you'll see they're encoded differently.


---

## Comment 2

> Username: jwakely  
> Created_at: 2021-01-18 15:08:55 UTC  
> Url: https://github.com/boostorg/units/pull/28#issuecomment-762308294  

FWIW, I see that this fix finally made it into a release with 1.75.0

---
