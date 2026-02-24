# #33 Internalize scope_guard from Boost.MultiIndex [Merged]

> Username: Mike-Devel  
> Created at: 2019-03-25 15:12:24 UTC  
> Updated at: 2019-03-26 07:21:54 UTC  
> Merged at: 2019-03-25 23:52:34 UTC  
> Closed at: 2019-03-25 23:52:34 UTC  
> Url: https://github.com/boostorg/signals2/pull/33  

scope_guard is an implementation detail of multi index and other libraries should not depend on it.  
  
I copy pased only the relevant parts used in boost signals.   
  
As a side-effect this reduces significantly the number of (indirect) dependencies of Boost.Signals2 (67-> 29) as seen by boostdep and package management tools like vcpkg and conan, because Boost.MultiIndex has a dependency on Boost.Serialization.    
  
An even better solution might be to use Boost.Scope_exit but I'm not familiar with that library and didn't want to mess with the Boost.Signals2 sources more than necessary. So this seemed to be the simples solution.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-03-25 18:26:00 UTC  
> Updated_at: 2019-03-25 18:27:50 UTC  
> Url: https://github.com/boostorg/signals2/pull/33#issuecomment-476322081  

# [Codecov](https://codecov.io/gh/boostorg/signals2/pull/33?src=pr&el=h1) Report  
> Merging [#33](https://codecov.io/gh/boostorg/signals2/pull/33?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/signals2/commit/4c1940709e8ed11da0b16e563b5fd3143e70d60a?src=pr&el=desc) will **decrease** coverage by `0.78%`.  
> The diff coverage is `0%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/signals2/pull/33/graphs/tree.svg?width=650&token=s6LljlwIKV&height=150&src=pr)](https://codecov.io/gh/boostorg/signals2/pull/33?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #33      +/-   ##  
===========================================  
- Coverage    53.13%   52.34%   -0.79%       
===========================================  
  Files           29       30       +1       
  Lines          862      875      +13       
  Branches       337      338       +1       
===========================================  
  Hits           458      458                
- Misses          91      104      +13       
  Partials       313      313  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/signals2/pull/33?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/signals2/detail/auto\_buffer.hpp](https://codecov.io/gh/boostorg/signals2/pull/33/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9zaWduYWxzMi9kZXRhaWwvYXV0b19idWZmZXIuaHBw) | `36.69% <0%> (ø)` | :arrow_up: |  
| [include/boost/signals2/detail/scope\_guard.hpp](https://codecov.io/gh/boostorg/signals2/pull/33/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9zaWduYWxzMi9kZXRhaWwvc2NvcGVfZ3VhcmQuaHBw) | `0% <0%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/signals2/pull/33?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/signals2/pull/33?src=pr&el=footer). Last update [4c19407...12c0d4b](https://codecov.io/gh/boostorg/signals2/pull/33?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
