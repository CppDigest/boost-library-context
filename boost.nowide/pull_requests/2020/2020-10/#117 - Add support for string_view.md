# #117 Add support for string_view [Merged]

> Username: Flamefire  
> Created at: 2020-10-05 16:20:28 UTC  
> Updated at: 2021-01-11 15:09:07 UTC  
> Merged at: 2021-01-11 15:09:02 UTC  
> Closed at: 2021-01-11 15:09:02 UTC  
> Url: https://github.com/boostorg/nowide/pull/117  

This adds generic support for `string_view` by providing a trait that detects `std::string`, `std::string_view` and similar classes (such as `boost::string_view` and `nostd::string_view`.  
  
Closes #33

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-10-05 17:36:15 UTC  
> Updated_at: 2021-01-11 14:40:05 UTC  
> Url: https://github.com/boostorg/nowide/pull/117#issuecomment-703780392  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/117?src=pr&el=h1) Report  
> Merging [#117](https://codecov.io/gh/boostorg/nowide/pull/117?src=pr&el=desc) (4592f23) into [develop](https://codecov.io/gh/boostorg/nowide/commit/de9a58d43b9d4bd9b6eae5e69a6da391dadfa625?el=desc) (de9a58d) will **increase** coverage by `0.34%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #117      +/-   ##  
===========================================  
+ Coverage    89.35%   89.69%   +0.34%       
===========================================  
  Files           28       28                
  Lines         2057     2077      +20       
  Branches       185      183       -2       
===========================================  
+ Hits          1838     1863      +25       
+ Misses         213      208       -5       
  Partials         6        6                
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/117?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [test/test\_traits.cpp](https://codecov.io/gh/boostorg/nowide/pull/117/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X3RyYWl0cy5jcHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/nowide/convert.hpp](https://codecov.io/gh/boostorg/nowide/pull/117/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvY29udmVydC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/nowide/utf/convert.hpp](https://codecov.io/gh/boostorg/nowide/pull/117/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvdXRmL2NvbnZlcnQuaHBw) | `100.00% <100.00%> (+3.12%)` | :arrow_up: |  
| [include/boost/nowide/utf/utf.hpp](https://codecov.io/gh/boostorg/nowide/pull/117/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvdXRmL3V0Zi5ocHA=) | `94.87% <100.00%> (+3.34%)` | :arrow_up: |  
| [test/test\_convert.cpp](https://codecov.io/gh/boostorg/nowide/pull/117/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2NvbnZlcnQuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_sets.hpp](https://codecov.io/gh/boostorg/nowide/pull/117/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X3NldHMuaHBw) | `91.66% <100.00%> (+0.36%)` | :arrow_up: |

---
