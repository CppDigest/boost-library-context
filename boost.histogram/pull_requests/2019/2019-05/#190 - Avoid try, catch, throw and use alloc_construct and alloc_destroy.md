# #190 Avoid try, catch, throw and use alloc_construct and alloc_destroy [Merged]

> Username: glenfe  
> Created at: 2019-05-12 16:27:54 UTC  
> Updated at: 2019-05-13 10:39:54 UTC  
> Merged at: 2019-05-13 06:42:54 UTC  
> Closed at: 2019-05-13 06:42:54 UTC  
> Url: https://github.com/boostorg/histogram/pull/190  

This change:  
* Uses RAII instead of try, catch, throw to achieve exception safety with or without exceptions enabled  
* Uses boost::alloc_construct and boost::alloc_destroy from Boost.Core to simplify the implementation  
  
Note that you don't need to use allocator_traits for allocate() and deallocate(). Every allocator is required to provide them.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-05-12 20:27:29 UTC  
> Updated_at: 2019-05-12 20:36:37 UTC  
> Url: https://github.com/boostorg/histogram/pull/190#issuecomment-491626609  

# [Codecov](https://codecov.io/gh/boostorg/histogram/pull/190?src=pr&el=h1) Report  
> Merging [#190](https://codecov.io/gh/boostorg/histogram/pull/190?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/histogram/commit/86021020143a2353265b2c3546da0f8d477d0667?src=pr&el=desc) will **decrease** coverage by `0.04%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/histogram/pull/190/graphs/tree.svg?width=650&token=maq3UUcyCe&height=150&src=pr)](https://codecov.io/gh/boostorg/histogram/pull/190?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #190      +/-   ##  
===========================================  
- Coverage      100%   99.95%   -0.05%       
===========================================  
  Files           44       44                
  Lines         2126     2123       -3       
===========================================  
- Hits          2126     2122       -4       
- Misses           0        1       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/histogram/pull/190?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/histogram/detail/variant.hpp](https://codecov.io/gh/boostorg/histogram/pull/190/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9oaXN0b2dyYW0vZGV0YWlsL3ZhcmlhbnQuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/histogram/unlimited\_storage.hpp](https://codecov.io/gh/boostorg/histogram/pull/190/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9oaXN0b2dyYW0vdW5saW1pdGVkX3N0b3JhZ2UuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/histogram/detail/large\_int.hpp](https://codecov.io/gh/boostorg/histogram/pull/190/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9oaXN0b2dyYW0vZGV0YWlsL2xhcmdlX2ludC5ocHA=) | `99.27% <0%> (-0.73%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/histogram/pull/190?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/histogram/pull/190?src=pr&el=footer). Last update [8602102...1d0bcd3](https://codecov.io/gh/boostorg/histogram/pull/190?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: HDembinski  
> Created_at: 2019-05-13 06:44:48 UTC  
> Url: https://github.com/boostorg/histogram/pull/190#issuecomment-491695994  

Beautiful solution, much better than using macros and makes better use of existing library code. Thank you!

---

## Comment 3

> Username: HDembinski  
> Created_at: 2019-05-13 10:39:54 UTC  
> Url: https://github.com/boostorg/histogram/pull/190#issuecomment-491770826  

> On 12. May 2019, at 18:43, Glen Fernandes <notifications@github.com> wrote:  
>   
> The failure appears to be that Appveyor is using Boost.Core from master instead of Boost.Core from develop. I'll merge Core develop to master later today.  
  
Yes, I need to fix the Appveyor build so that it correctly uses the develop and master branches, as I am doing already in Travis.

---
