# #85 Explicitly delete non implemented private copy operator to avoid gcc … [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2019-04-17 21:28:54 UTC  
> Updated at: 2019-05-28 23:51:12 UTC  
> Merged at: 2019-05-28 23:51:12 UTC  
> Closed at: 2019-05-28 23:51:12 UTC  
> Url: https://github.com/boostorg/iostreams/pull/85  

…9 -Wdeprecated-copy warning.  
  
Hi,  
  
Gcc 9 is unhappy when the implicit copy constructor is used but when an explicit copy operator is declared, even when it's declared but not defined on purpose to prevent copy assignment. Make gcc happy by explicitly delete them in C++ >= 11.  
  
Cheers,  
Romain

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-04-20 14:30:28 UTC  
> Updated_at: 2019-05-28 18:21:26 UTC  
> Url: https://github.com/boostorg/iostreams/pull/85#issuecomment-485130366  

# [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/85?src=pr&el=h1) Report  
> Merging [#85](https://codecov.io/gh/boostorg/iostreams/pull/85?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/iostreams/commit/50b4f003e5205db1243e4d2469e6f795fb57dc45?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/iostreams/pull/85/graphs/tree.svg?width=650&token=LBEfwtNfca&height=150&src=pr)](https://codecov.io/gh/boostorg/iostreams/pull/85?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #85   +/-   ##  
========================================  
  Coverage    69.04%   69.04%             
========================================  
  Files           80       80             
  Lines         3437     3437             
  Branches      1025     1025             
========================================  
  Hits          2373     2373             
  Misses         451      451             
  Partials       613      613  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/iostreams/pull/85?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/iostreams/detail/functional.hpp](https://codecov.io/gh/boostorg/iostreams/pull/85/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvZGV0YWlsL2Z1bmN0aW9uYWwuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [...boost/iostreams/detail/adapter/concept\_adapter.hpp](https://codecov.io/gh/boostorg/iostreams/pull/85/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvZGV0YWlsL2FkYXB0ZXIvY29uY2VwdF9hZGFwdGVyLmhwcA==) | `67.27% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/iostreams/pull/85?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/85?src=pr&el=footer). Last update [50b4f00...5e5960d](https://codecov.io/gh/boostorg/iostreams/pull/85?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Changes requested]

> Username: jeking3  
> Created_at: 2019-05-03 16:39:17 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/iostreams/pull/85#pullrequestreview-233564104  

Could you rebase this on develop for force push? None of our jobs build with gcc-9 right now, but we can prove everything works with other compilers at least. Thanks!

---

## Comment 3

> Username: Romain-Geissler-1A  
> Created_at: 2019-05-03 16:54:55 UTC  
> Url: https://github.com/boostorg/iostreams/pull/85#issuecomment-489165508  

Hi,  
  
I just did the rebase.  
  
Cheers,  
Romain

---
