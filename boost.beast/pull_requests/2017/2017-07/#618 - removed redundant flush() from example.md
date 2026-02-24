# #618 removed redundant flush() from example [Closed]

> Username: akrzemi1  
> Created at: 2017-07-07 12:36:58 UTC  
> Updated at: 2017-07-07 13:02:36 UTC  
> Closed at: 2017-07-07 12:40:30 UTC  
> Url: https://github.com/boostorg/beast/pull/618  

`<<std::endl` already flushes the stream; no need for a separate call.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-07-07 12:40:30 UTC  
> Url: https://github.com/boostorg/beast/pull/618#issuecomment-313670243  

Part of **v74** now - thank you!

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2017-07-07 13:02:17 UTC  
> Updated_at: 2017-07-07 13:02:36 UTC  
> Url: https://github.com/boostorg/beast/pull/618#issuecomment-313674838  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/618?src=pr&el=h1) Report  
> Merging [#618](https://codecov.io/gh/vinniefalco/Beast/pull/618?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/89c416cde64de3265299ced216a6eef9428c2a51?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/618/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/vinniefalco/Beast/pull/618?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff           @@  
##           master     #618   +/-   ##  
=======================================  
  Coverage   93.84%   93.84%             
=======================================  
  Files          94       94             
  Lines        7476     7476             
=======================================  
  Hits         7016     7016             
  Misses        460      460  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/618?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/618?src=pr&el=footer). Last update [89c416c...f72a9f6](https://codecov.io/gh/vinniefalco/Beast/pull/618?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
