# #49 Fix warning: unused variable ‘init’ [-Wunused-variable] [Closed]

> Username: mloskot  
> Created at: 2020-03-07 22:41:49 UTC  
> Updated at: 2020-03-08 01:01:13 UTC  
> Closed at: 2020-03-08 00:08:15 UTC  
> Url: https://github.com/boostorg/json/pull/49  

Pedantic avoidance of  
  
```  
../../libs/json/test/value_ref.cpp:177:29: error: expected primary-expression before ‘init’  
  177 |             (void)init_list init =  
      |                             ^~~~  
```

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-07 23:15:25 UTC  
> Updated_at: 2020-03-07 23:16:44 UTC  
> Url: https://github.com/boostorg/json/pull/49#issuecomment-596147118  

# [Codecov](https://codecov.io/gh/vinniefalco/json/pull/49?src=pr&el=h1) Report  
> Merging [#49](https://codecov.io/gh/vinniefalco/json/pull/49?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/json/commit/37d0a09242bf0d116c0cd69415034ff93fd9cc02?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/json/pull/49/graphs/tree.svg?width=650&token=HNiMmIjyKi&height=150&src=pr)](https://codecov.io/gh/vinniefalco/json/pull/49?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #49   +/-   ##  
========================================  
  Coverage    99.09%   99.09%             
========================================  
  Files           57       57             
  Lines         4854     4854             
========================================  
  Hits          4810     4810             
  Misses          44       44  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/json/pull/49?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/json/pull/49?src=pr&el=footer). Last update [37d0a09...2318d56](https://codecov.io/gh/vinniefalco/json/pull/49?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-03-08 00:08:15 UTC  
> Url: https://github.com/boostorg/json/pull/49#issuecomment-596150665  

Thanks!

---
