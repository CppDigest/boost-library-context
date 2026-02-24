# #436 Fix file_body::get() not setting the more flag correctly [Closed]

> Username: niklas88  
> Created at: 2017-06-08 08:14:28 UTC  
> Updated at: 2017-06-10 01:09:12 UTC  
> Closed at: 2017-06-10 01:09:12 UTC  
> Url: https://github.com/boostorg/beast/pull/436  

The check for more data actually checked if the end of data was reached  
so it was logically inverted.  
  
This fixes #434

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-08 08:43:20 UTC  
> Updated_at: 2017-06-08 08:44:20 UTC  
> Url: https://github.com/boostorg/beast/pull/436#issuecomment-307039368  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/436?src=pr&el=h1) Report  
> Merging [#436](https://codecov.io/gh/vinniefalco/Beast/pull/436?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/a2c1117fc0c8125f88d5cfcf14e91f9963441e45?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/436/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/vinniefalco/Beast/pull/436?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff           @@  
##           master     #436   +/-   ##  
=======================================  
  Coverage   93.79%   93.79%             
=======================================  
  Files          92       92             
  Lines        6723     6723             
=======================================  
  Hits         6306     6306             
  Misses        417      417  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/436?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/436?src=pr&el=footer). Last update [a2c1117...a4e051e](https://codecov.io/gh/vinniefalco/Beast/pull/436?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-06-08 12:47:33 UTC  
> Url: https://github.com/boostorg/beast/pull/436#issuecomment-307092678  

Thanks! This will go into **v51**

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-06-10 01:09:12 UTC  
> Url: https://github.com/boostorg/beast/pull/436#issuecomment-307532217  

Merged to **v51**

---
