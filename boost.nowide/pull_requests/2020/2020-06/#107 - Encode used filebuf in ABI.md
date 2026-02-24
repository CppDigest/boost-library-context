# #107 Encode used filebuf in ABI [Merged]

> Username: Flamefire  
> Created at: 2020-06-15 14:33:22 UTC  
> Updated at: 2020-06-16 15:43:55 UTC  
> Merged at: 2020-06-16 15:43:51 UTC  
> Closed at: 2020-06-16 15:43:51 UTC  
> Url: https://github.com/boostorg/nowide/pull/107  

Avoids ODR violations if some code defines BOOST_NOWIDE_USE_FILEBUF_REPLACEMENT and some does not although this is  
not officially supported. This change does an effort in allowing it for  
some use cases depending on the ABI used.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-06-15 17:04:08 UTC  
> Updated_at: 2020-06-15 17:07:32 UTC  
> Url: https://github.com/boostorg/nowide/pull/107#issuecomment-644256164  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/107?src=pr&el=h1) Report  
> Merging [#107](https://codecov.io/gh/boostorg/nowide/pull/107?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/nowide/commit/46d37203b6d12ec7a1b3d3d0f316f3c7199d7665&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #107   +/-   ##  
========================================  
  Coverage    90.17%   90.17%             
========================================  
  Files           24       24             
  Lines         2524     2524             
  Branches       185      185             
========================================  
  Hits          2276     2276             
  Misses         242      242             
  Partials         6        6             
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/107?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/nowide/fstream.hpp](https://codecov.io/gh/boostorg/nowide/pull/107/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZnN0cmVhbS5ocHA=) | `98.51% <ø> (ø)` | |

---
