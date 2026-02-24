# #77 Disable getrandom provider on Android prior to API version 28. [Merged]

> Username: Lastique  
> Created at: 2018-08-15 21:25:33 UTC  
> Updated at: 2018-08-19 14:39:53 UTC  
> Merged at: 2018-08-19 14:36:01 UTC  
> Closed at: 2018-08-19 14:36:01 UTC  
> Url: https://github.com/boostorg/uuid/pull/77  

Android supports getrandom and getentropy functions only since API version 28.  
  
As part of the fix converted Linux/Android platform detection from Boost.Predef  
to direct checks of platform-specific macros. This is to work around  
Boost.Predef problem described in [1] - depending on header inclusion order  
Boost.Predef may indicate Linux or Android. When that problem is fixed we  
may change back to Boost.Predef.  
  
Fixes https://github.com/boostorg/uuid/issues/76.  
  
[1]: https://github.com/boostorg/predef/issues/81#issuecomment-413329061

---

## Review 1 [Commented]

> Username: jeking3  
> Created_at: 2018-08-15 23:54:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/77#pullrequestreview-146660507  

📁 include/boost/uuid/detail/random_provider_detect_platform.hpp

```diff
  18 |+ // Note: Don't use Boost.Predef to detect Linux and Android as it may give different results depending on header inclusion order.
  19 |+ // https://github.com/boostorg/predef/issues/81#issuecomment-413329061
  20 |+ #if (defined(__linux__) || defined(__linux) || defined(linux)) && (!defined(__ANDROID__) || (defined(__ANDROID__) && __ANDROID_API__ >= 28))
```

> Username: jeking3  
> Created_at: 2018-08-15 23:54:04 UTC  
> Updated_at: 2018-08-16 08:12:17 UTC  
> Url: https://github.com/boostorg/uuid/pull/77#discussion_r210444032  

Too bad we have to repeat this sequence twice. :|


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2018-08-16 01:08:37 UTC  
> Updated_at: 2018-08-16 09:04:17 UTC  
> Url: https://github.com/boostorg/uuid/pull/77#issuecomment-413389384  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/77?src=pr&el=h1) Report  
> Merging [#77](https://codecov.io/gh/boostorg/uuid/pull/77?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/uuid/commit/9522a13919c75ceade063c7e8d6bc785f2814416?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/77/graphs/tree.svg?height=150&width=650&token=6falIr5npV&src=pr)](https://codecov.io/gh/boostorg/uuid/pull/77?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #77   +/-   ##  
========================================  
  Coverage    79.78%   79.78%             
========================================  
  Files           13       13             
  Lines          638      638             
  Branches       154      154             
========================================  
  Hits           509      509             
  Misses          17       17             
  Partials       112      112  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/77?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/77?src=pr&el=footer). Last update [9522a13...c8eaad5](https://codecov.io/gh/boostorg/uuid/pull/77?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: Lastique  
> Created_at: 2018-08-16 08:13:01 UTC  
> Url: https://github.com/boostorg/uuid/pull/77#issuecomment-413463225  

I've simplified preprocessor checks and removed duplication.

---
