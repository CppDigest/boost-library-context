# #106 Make UTF conversion functions public [Merged]

> Username: Flamefire  
> Created at: 2020-06-15 14:33:10 UTC  
> Updated at: 2020-06-16 21:10:06 UTC  
> Merged at: 2020-06-16 21:10:03 UTC  
> Closed at: 2020-06-16 21:10:03 UTC  
> Url: https://github.com/boostorg/nowide/pull/106  

Those functions and types might be useful in situations involving templates over the used charset/encoding.  
  
So while this is not a maximally performant solution it can be used outside of Boost.Nowide too which is now "blessed" by moving it out of the detail namespace into `boost::nowide::utf`

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-06-15 15:50:31 UTC  
> Updated_at: 2020-06-15 16:20:29 UTC  
> Url: https://github.com/boostorg/nowide/pull/106#issuecomment-644217249  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/106?src=pr&el=h1) Report  
> Merging [#106](https://codecov.io/gh/boostorg/nowide/pull/106?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/nowide/commit/46d37203b6d12ec7a1b3d3d0f316f3c7199d7665&el=desc) will **not change** coverage.  
> The diff coverage is `92.89%`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #106   +/-   ##  
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
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/106?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [src/iostream.cpp](https://codecov.io/gh/boostorg/nowide/pull/106/diff?src=pr&el=tree#diff-c3JjL2lvc3RyZWFtLmNwcA==) | `35.94% <0.00%> (ø)` | |  
| [test/test\_iostream.cpp](https://codecov.io/gh/boostorg/nowide/pull/106/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2lvc3RyZWFtLmNwcA==) | `37.50% <ø> (ø)` | |  
| [test/test\_system.cpp](https://codecov.io/gh/boostorg/nowide/pull/106/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X3N5c3RlbS5jcHA=) | `98.11% <ø> (ø)` | |  
| [include/boost/nowide/utf/utf.hpp](https://codecov.io/gh/boostorg/nowide/pull/106/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvdXRmL3V0Zi5ocHA=) | `92.75% <92.75%> (ø)` | |  
| [include/boost/nowide/utf8\_codecvt.hpp](https://codecov.io/gh/boostorg/nowide/pull/106/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvdXRmOF9jb2RlY3Z0LmhwcA==) | `91.35% <94.44%> (ø)` | |  
| [include/boost/nowide/utf/convert.hpp](https://codecov.io/gh/boostorg/nowide/pull/106/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvdXRmL2NvbnZlcnQuaHBw) | `97.50% <97.50%> (ø)` | |  
| [include/boost/nowide/convert.hpp](https://codecov.io/gh/boostorg/nowide/pull/106/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvY29udmVydC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/nowide/stackstring.hpp](https://codecov.io/gh/boostorg/nowide/pull/106/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvc3RhY2tzdHJpbmcuaHBw) | `100.00% <100.00%> (ø)` | |

---
