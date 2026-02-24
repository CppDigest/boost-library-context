# #97 Remove deprecated inheritance from std::iterator [Merged]

> Username: poelmanc  
> Created at: 2020-08-03 22:35:48 UTC  
> Updated at: 2020-08-04 21:20:40 UTC  
> Merged at: 2020-08-04 21:20:40 UTC  
> Closed at: 2020-08-04 21:20:40 UTC  
> Url: https://github.com/boostorg/ublas/pull/97  

std::iterator was deprecated in C++17 and removed in C++20. I replaced the inheritance with the 5 equivalent typedefs, even though they're not all used by ublas, for compatibility in case clients depend on them.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-08-03 22:51:04 UTC  
> Url: https://github.com/boostorg/ublas/pull/97#issuecomment-668278655  

# [Codecov](https://codecov.io/gh/boostorg/ublas/pull/97?src=pr&el=h1) Report  
> Merging [#97](https://codecov.io/gh/boostorg/ublas/pull/97?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/ublas/commit/7cc02bf0a10ad1820db83e5d4ed19b0510da2f1e&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ublas/pull/97/graphs/tree.svg?width=650&height=150&src=pr&token=CShoBArRWq)](https://codecov.io/gh/boostorg/ublas/pull/97?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #97   +/-   ##  
========================================  
  Coverage    95.33%   95.33%             
========================================  
  Files           22       22             
  Lines         1223     1223             
========================================  
  Hits          1166     1166             
  Misses          57       57             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ublas/pull/97?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ublas/pull/97?src=pr&el=footer). Last update [7cc02bf...87e838d](https://codecov.io/gh/boostorg/ublas/pull/97?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
