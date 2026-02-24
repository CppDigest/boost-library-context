# #120 Avoid boost::fs::unique [Merged]

> Username: Flamefire  
> Created at: 2020-11-03 10:03:32 UTC  
> Updated at: 2020-11-03 12:22:36 UTC  
> Merged at: 2020-11-03 12:22:33 UTC  
> Closed at: 2020-11-03 12:22:33 UTC  
> Url: https://github.com/boostorg/nowide/pull/120  

This seems to lead to a "boost::filesystem::status: Function not implemented" error in some environments (e.g. Docker), although it is unclear why exactly. As this is not really needed here the executable name can be used instead.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-11-03 11:59:52 UTC  
> Url: https://github.com/boostorg/nowide/pull/120#issuecomment-721073930  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/120?src=pr&el=h1) Report  
> Merging [#120](https://codecov.io/gh/boostorg/nowide/pull/120?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/nowide/commit/5bf123ff3c462fa477c90d171fe35ce51947a0d9?el=desc) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #120   +/-   ##  
========================================  
  Coverage    89.35%   89.35%             
========================================  
  Files           28       28             
  Lines         2057     2057             
  Branches       185      185             
========================================  
  Hits          1838     1838             
  Misses         213      213             
  Partials         6        6             
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/120?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [test/test\_fs.cpp](https://codecov.io/gh/boostorg/nowide/pull/120/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2ZzLmNwcA==) | `100.00% <100.00%> (ø)` | |

---
