# #159 Simplify pbackfail allowing only limited putback [Merged]

> Username: Flamefire  
> Created at: 2022-06-09 15:59:52 UTC  
> Updated at: 2022-06-10 17:27:38 UTC  
> Merged at: 2022-06-10 17:27:35 UTC  
> Closed at: 2022-06-10 17:27:35 UTC  
> Url: https://github.com/boostorg/nowide/pull/159  

There are too many corner cases in putback handling.  
As use of that is rare anyway do the bare minimum and only allow putback into the current (read) buffer.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-09 20:53:25 UTC  
> Updated_at: 2022-06-10 13:50:53 UTC  
> Url: https://github.com/boostorg/nowide/pull/159#issuecomment-1151613153  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/159?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#159](https://codecov.io/gh/boostorg/nowide/pull/159?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f2eec77) into [develop](https://codecov.io/gh/boostorg/nowide/commit/1b9a56a298fcafd8b8b3007743e9bad0419434f1?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1b9a56a) will **increase** coverage by `0.07%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #159      +/-   ##  
===========================================  
+ Coverage    99.37%   99.44%   +0.07%       
===========================================  
  Files           33       33                
  Lines         3180     3232      +52       
===========================================  
+ Hits          3160     3214      +54       
+ Misses          20       18       -2       
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/159?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/nowide/filebuf.hpp](https://codecov.io/gh/boostorg/nowide/pull/159/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvZmlsZWJ1Zi5ocHA=) | `94.82% <100.00%> (+0.63%)` | :arrow_up: |  
| [test/test\_filebuf.cpp](https://codecov.io/gh/boostorg/nowide/pull/159/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2ZpbGVidWYuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_fstream\_special.cpp](https://codecov.io/gh/boostorg/nowide/pull/159/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2ZzdHJlYW1fc3BlY2lhbC5jcHA=) | `100.00% <100.00%> (ø)` | |

---
