# #155 Fix use of fflush and enhance filebuf tests [Merged]

> Username: Flamefire  
> Created at: 2022-05-23 13:54:52 UTC  
> Updated at: 2022-05-25 12:13:41 UTC  
> Merged at: 2022-05-25 12:13:37 UTC  
> Closed at: 2022-05-25 12:13:37 UTC  
> Url: https://github.com/boostorg/nowide/pull/155  

Only call fflush after actual output  
  
The function is (sometimes?) described as  
> For input streams (and for update streams on which the last operation was input), the behavior is undefined.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-05-25 11:24:22 UTC  
> Updated_at: 2022-05-25 11:31:25 UTC  
> Url: https://github.com/boostorg/nowide/pull/155#issuecomment-1137121257  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/155?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#155](https://codecov.io/gh/boostorg/nowide/pull/155?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c16c196) into [develop](https://codecov.io/gh/boostorg/nowide/commit/c28aef3213f400bab268c5305bd8426cdd74840d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c28aef3) will **increase** coverage by `0.02%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #155      +/-   ##  
===========================================  
+ Coverage    99.34%   99.37%   +0.02%       
===========================================  
  Files           33       33                
  Lines         3051     3180     +129       
===========================================  
+ Hits          3031     3160     +129       
  Misses          20       20                
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/155?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/nowide/filebuf.hpp](https://codecov.io/gh/boostorg/nowide/pull/155/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZmlsZWJ1Zi5ocHA=) | `94.19% <100.00%> (+0.02%)` | :arrow_up: |  
| [test/test\_filebuf.cpp](https://codecov.io/gh/boostorg/nowide/pull/155/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2ZpbGVidWYuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_system.cpp](https://codecov.io/gh/boostorg/nowide/pull/155/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N5c3RlbS5jcHA=) | `100.00% <100.00%> (ø)` | |

---
