# #151 Improve tests [Merged]

> Username: Flamefire  
> Created at: 2022-03-16 17:30:34 UTC  
> Updated at: 2022-03-18 10:22:23 UTC  
> Merged at: 2022-03-18 10:22:19 UTC  
> Closed at: 2022-03-18 10:22:20 UTC  
> Url: https://github.com/boostorg/nowide/pull/151  

- Add reproducer for #150 (reopening an ofstream after close())  
- Skip flaky iostream integration test  
- Avoid compiling and running test_traits 2 times by merging the boost and std filesystem parts into the same binary  
- Enable c++14/17 explicitly on Appveyor cmake-tests to make sure e.g. the traits test are run with those testing the std::filesystem parts  
- Refactoring (use nullptr, remove unused private member function)  
- Fix return value of `filebuf::sync()` when fflush fails  
  
Closes #150

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-03-17 12:44:13 UTC  
> Updated_at: 2022-03-17 20:26:06 UTC  
> Url: https://github.com/boostorg/nowide/pull/151#issuecomment-1070882677  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/151?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#151](https://codecov.io/gh/boostorg/nowide/pull/151?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b0f2767) into [develop](https://codecov.io/gh/boostorg/nowide/commit/1e6badc0c1a0929463187c560f238e0a7ef22caf?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1e6badc) will **increase** coverage by `0.00%`.  
> The diff coverage is `97.33%`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #151   +/-   ##  
========================================  
  Coverage    99.33%   99.34%             
========================================  
  Files           33       33             
  Lines         3021     3051   +30       
========================================  
+ Hits          3001     3031   +30       
  Misses          20       20             
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/151?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_traits.cpp](https://codecov.io/gh/boostorg/nowide/pull/151/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3RyYWl0cy5jcHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/nowide/filebuf.hpp](https://codecov.io/gh/boostorg/nowide/pull/151/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZmlsZWJ1Zi5ocHA=) | `94.16% <90.47%> (+0.02%)` | :arrow_up: |  
| [include/boost/nowide/args.hpp](https://codecov.io/gh/boostorg/nowide/pull/151/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvYXJncy5ocHA=) | `95.83% <100.00%> (ø)` | |  
| [include/boost/nowide/stackstring.hpp](https://codecov.io/gh/boostorg/nowide/pull/151/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvc3RhY2tzdHJpbmcuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/nowide/utf/convert.hpp](https://codecov.io/gh/boostorg/nowide/pull/151/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvdXRmL2NvbnZlcnQuaHBw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_fstream\_special.cpp](https://codecov.io/gh/boostorg/nowide/pull/151/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2ZzdHJlYW1fc3BlY2lhbC5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [test/test\_iostream.cpp](https://codecov.io/gh/boostorg/nowide/pull/151/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2lvc3RyZWFtLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_ofstream.cpp](https://codecov.io/gh/boostorg/nowide/pull/151/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X29mc3RyZWFtLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_stackstring.cpp](https://codecov.io/gh/boostorg/nowide/pull/151/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N0YWNrc3RyaW5nLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_stdio.cpp](https://codecov.io/gh/boostorg/nowide/pull/151/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N0ZGlvLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| ... and [2 more](https://codecov.io/gh/boostorg/nowide/pull/151/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |

---
