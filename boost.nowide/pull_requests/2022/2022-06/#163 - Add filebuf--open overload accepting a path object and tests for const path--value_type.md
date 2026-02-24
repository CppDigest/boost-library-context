# #163 Add filebuf::open overload accepting a path object and tests for const path::value_type* [Merged]

> Username: Flamefire  
> Created at: 2022-06-25 14:54:40 UTC  
> Updated at: 2022-06-26 18:20:42 UTC  
> Merged at: 2022-06-26 18:20:38 UTC  
> Closed at: 2022-06-26 18:20:38 UTC  
> Url: https://github.com/boostorg/nowide/pull/163  

Add filebuf::open overload accepting a path object to catch up with C++17 `std` classes  
  
Add some `static_assert`s checking that the filebuf and fstream classes  
can be used with `const *::filesystem::path::value_type*` and `::filesystem::path&`   
Closes #153

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-25 18:04:30 UTC  
> Updated_at: 2022-06-25 18:07:53 UTC  
> Url: https://github.com/boostorg/nowide/pull/163#issuecomment-1166335710  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/163?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#163](https://codecov.io/gh/boostorg/nowide/pull/163?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6c0847c) into [develop](https://codecov.io/gh/boostorg/nowide/commit/52f14e579f312e43c05836d5293b28566b4a09a2?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (52f14e5) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #163   +/-   ##  
========================================  
  Coverage    99.46%   99.46%             
========================================  
  Files           33       33             
  Lines         3385     3386    +1       
========================================  
+ Hits          3367     3368    +1       
  Misses          18       18             
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/163?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/nowide/filebuf.hpp](https://codecov.io/gh/boostorg/nowide/pull/163/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZmlsZWJ1Zi5ocHA=) | `95.65% <ø> (ø)` | |  
| [test/test\_traits.cpp](https://codecov.io/gh/boostorg/nowide/pull/163/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3RyYWl0cy5jcHA=) | `100.00% <100.00%> (ø)` | |

---
