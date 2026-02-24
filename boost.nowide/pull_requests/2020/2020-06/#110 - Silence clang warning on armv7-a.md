# #110 Silence clang warning on armv7-a [Merged]

> Username: Flamefire  
> Created at: 2020-06-30 07:38:12 UTC  
> Updated at: 2020-07-10 08:18:41 UTC  
> Merged at: 2020-07-10 08:18:37 UTC  
> Closed at: 2020-07-10 08:18:37 UTC  
> Url: https://github.com/boostorg/nowide/pull/110  

```  
In file included from ../libs/nowide/test/test_fstream.cpp:10:  
In file included from ../boost/nowide/fstream.hpp:12:  
../boost/nowide/filebuf.hpp:368:20: error: result of comparison of constant 9223372036854775807 with expression of type 'const int' is always false [-Werror,-Wtautological-constant-out-of-range-compare]  
            if(off > std::numeric_limits<std::streamoff>::max())  
               ~~~ ^ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  
```

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-07-10 08:16:12 UTC  
> Url: https://github.com/boostorg/nowide/pull/110#issuecomment-656549421  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/110?src=pr&el=h1) Report  
> Merging [#110](https://codecov.io/gh/boostorg/nowide/pull/110?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/nowide/commit/84dfbff44a293cedd642e1f181d0dcfc3cfe85ce&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #110   +/-   ##  
========================================  
  Coverage    90.31%   90.31%             
========================================  
  Files           27       27             
  Lines         2561     2561             
  Branches       185      185             
========================================  
  Hits          2313     2313             
  Misses         242      242             
  Partials         6        6             
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/110?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/nowide/filebuf.hpp](https://codecov.io/gh/boostorg/nowide/pull/110/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZmlsZWJ1Zi5ocHA=) | `82.28% <ø> (ø)` | |

---
