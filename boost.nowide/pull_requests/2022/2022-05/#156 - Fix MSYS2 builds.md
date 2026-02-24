# #156 Fix MSYS2 builds [Merged]

> Username: Flamefire  
> Created at: 2022-05-23 15:07:59 UTC  
> Updated at: 2022-05-24 11:46:26 UTC  
> Merged at: 2022-05-24 11:46:23 UTC  
> Closed at: 2022-05-24 11:46:23 UTC  
> Url: https://github.com/boostorg/nowide/pull/156  

GCC triggers a false positive:  
  
> D:/a/_temp/msys64/mingw32/include/c++/12.1.0/bits/char_traits.h:431:56: error: 'void* __builtin_memcpy(void*, const void*, unsigned int)' accessing 2147483650 or more bytes at offsets [2, 2147483647] and 1 overlaps 2147483653 bytes at offset -3 [-Werror=restrict]

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-05-24 10:13:44 UTC  
> Updated_at: 2022-05-24 10:16:45 UTC  
> Url: https://github.com/boostorg/nowide/pull/156#issuecomment-1135715920  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/156?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#156](https://codecov.io/gh/boostorg/nowide/pull/156?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e0511bd) into [develop](https://codecov.io/gh/boostorg/nowide/commit/c28aef3213f400bab268c5305bd8426cdd74840d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c28aef3) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #156   +/-   ##  
========================================  
  Coverage    99.34%   99.34%             
========================================  
  Files           33       33             
  Lines         3051     3051             
========================================  
  Hits          3031     3031             
  Misses          20       20             
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/156?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_codecvt.cpp](https://codecov.io/gh/boostorg/nowide/pull/156/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVjdnQuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_system.cpp](https://codecov.io/gh/boostorg/nowide/pull/156/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N5c3RlbS5jcHA=) | `100.00% <100.00%> (ø)` | |

---
