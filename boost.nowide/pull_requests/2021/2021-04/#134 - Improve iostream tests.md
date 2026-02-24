# #134 Improve iostream tests [Merged]

> Username: Flamefire  
> Created at: 2021-04-23 13:05:12 UTC  
> Updated at: 2021-05-05 13:30:16 UTC  
> Merged at: 2021-05-05 13:30:13 UTC  
> Closed at: 2021-05-05 13:30:13 UTC  
> Url: https://github.com/boostorg/nowide/pull/134  

Add extensive tests for the iostreams using mocked buffers which abstract the Windows API  
  
Make sure the iostreams are initialized as early as possible using compiler specific attribute/pragma

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-04-29 12:21:08 UTC  
> Updated_at: 2021-05-01 17:11:30 UTC  
> Url: https://github.com/boostorg/nowide/pull/134#issuecomment-829192433  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/134?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#134](https://codecov.io/gh/boostorg/nowide/pull/134?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2b12d65) into [develop](https://codecov.io/gh/boostorg/nowide/commit/c8a9cb0fc23b1b594180ce4c0b8ebb3b1f5080dd?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c8a9cb0) will **increase** coverage by `4.62%`.  
> The diff coverage is `97.95%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #134      +/-   ##  
===========================================  
+ Coverage    91.64%   96.27%   +4.62%       
===========================================  
  Files           33       34       +1       
  Lines         2491     2631     +140       
  Branches       185      215      +30       
===========================================  
+ Hits          2283     2533     +250       
+ Misses         202       92     -110       
  Partials         6        6                
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/134?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_sets.hpp](https://codecov.io/gh/boostorg/nowide/pull/134/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3NldHMuaHBw) | `91.66% <ø> (ø)` | |  
| [src/iostream.cpp](https://codecov.io/gh/boostorg/nowide/pull/134/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2lvc3RyZWFtLmNwcA==) | `76.92% <69.23%> (+42.03%)` | :arrow_up: |  
| [test/test\_iostream.cpp](https://codecov.io/gh/boostorg/nowide/pull/134/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2lvc3RyZWFtLmNwcA==) | `99.00% <98.97%> (+64.45%)` | :arrow_up: |  
| [src/console\_buffer.cpp](https://codecov.io/gh/boostorg/nowide/pull/134/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2NvbnNvbGVfYnVmZmVyLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/nowide/utf/utf.hpp](https://codecov.io/gh/boostorg/nowide/pull/134/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvdXRmL3V0Zi5ocHA=) | `95.86% <0.00%> (+0.82%)` | :arrow_up: |

---
