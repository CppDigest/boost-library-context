# #95 cin fixes [Merged]

> Username: Flamefire  
> Created at: 2020-05-04 18:33:38 UTC  
> Updated at: 2020-05-07 07:51:57 UTC  
> Merged at: 2020-05-07 07:51:53 UTC  
> Closed at: 2020-05-07 07:51:53 UTC  
> Url: https://github.com/boostorg/nowide/pull/95  

- Support cin.sync  
- Ignore CR (\r)  
- Treat CTRL+Z at beginning of line as EOF

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-05-06 17:54:00 UTC  
> Url: https://github.com/boostorg/nowide/pull/95#issuecomment-624797225  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/95?src=pr&el=h1) Report  
> Merging [#95](https://codecov.io/gh/boostorg/nowide/pull/95?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/nowide/commit/360e55f782bb5cd0ee1b690622c1611306d0a1f3&el=desc) will **decrease** coverage by `1.18%`.  
> The diff coverage is `18.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #95      +/-   ##  
===========================================  
- Coverage    91.33%   90.14%   -1.19%       
===========================================  
  Files           23       23                
  Lines         2481     2507      +26       
  Branches       186      186                
===========================================  
- Hits          2266     2260       -6       
- Misses         209      241      +32       
  Partials         6        6                
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/95?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [test/test\_iostream.cpp](https://codecov.io/gh/boostorg/nowide/pull/95/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2lvc3RyZWFtLmNwcA==) | `37.50% <0.00%> (-10.50%)` | :arrow_down: |  
| [src/iostream.cpp](https://codecov.io/gh/boostorg/nowide/pull/95/diff?src=pr&el=tree#diff-c3JjL2lvc3RyZWFtLmNwcA==) | `35.94% <25.00%> (-7.32%)` | :arrow_down: |  
| [include/boost/nowide/args.hpp](https://codecov.io/gh/boostorg/nowide/pull/95/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvYXJncy5ocHA=) | `97.26% <0.00%> (ø)` | |

---
