# #58 Fix CMake configuration for tests [Closed]

> Username: mloskot  
> Created at: 2020-03-14 01:09:05 UTC  
> Updated at: 2020-03-14 20:37:19 UTC  
> Closed at: 2020-03-14 20:35:30 UTC  
> Url: https://github.com/boostorg/json/pull/58  

A couple of things are broken that basically make `ctest` is a no-op, always:  
  
```  
Test project /home/runner/work/boost-json/boost-json/build  
No tests were found!!!  
```  
  
Mind you, it is a success (`0` return code)!  
  
This PR fixes all of them.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-14 01:41:03 UTC  
> Updated_at: 2020-03-14 01:57:09 UTC  
> Url: https://github.com/boostorg/json/pull/58#issuecomment-598994450  

# [Codecov](https://codecov.io/gh/vinniefalco/json/pull/58?src=pr&el=h1) Report  
> Merging [#58](https://codecov.io/gh/vinniefalco/json/pull/58?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/json/commit/8d13f6feebde9daca146bc453d0d70e0db3532dd&el=desc) will **not change** coverage by `%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/json/pull/58/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/vinniefalco/json/pull/58?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #58   +/-   ##  
========================================  
  Coverage    99.09%   99.09%             
========================================  
  Files           57       57             
  Lines         4861     4861             
========================================  
  Hits          4817     4817             
  Misses          44       44             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/json/pull/58?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/json/pull/58?src=pr&el=footer). Last update [8d13f6f...d835fa5](https://codecov.io/gh/vinniefalco/json/pull/58?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-03-14 20:35:30 UTC  
> Url: https://github.com/boostorg/json/pull/58#issuecomment-599131924  

Merged, thanks!

---
