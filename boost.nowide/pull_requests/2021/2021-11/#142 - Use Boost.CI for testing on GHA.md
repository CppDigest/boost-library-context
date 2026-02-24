# #142 Use Boost.CI for testing on GHA [Merged]

> Username: Flamefire  
> Created at: 2021-11-18 18:07:19 UTC  
> Updated at: 2021-12-01 11:27:24 UTC  
> Merged at: 2021-12-01 11:27:22 UTC  
> Closed at: 2021-12-01 11:27:22 UTC  
> Url: https://github.com/boostorg/nowide/pull/142  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-11-22 20:53:16 UTC  
> Updated_at: 2021-11-30 21:36:15 UTC  
> Url: https://github.com/boostorg/nowide/pull/142#issuecomment-975908908  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/142?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#142](https://codecov.io/gh/boostorg/nowide/pull/142?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5114e58) into [develop](https://codecov.io/gh/boostorg/nowide/commit/ee17695de1b67875f17b534f6b2a7ea32597e6f3?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ee17695) will **increase** coverage by `0.38%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #142      +/-   ##  
===========================================  
+ Coverage    98.68%   99.07%   +0.38%       
===========================================  
  Files           33       33                
  Lines         2822     2924     +102       
===========================================  
+ Hits          2785     2897     +112       
+ Misses          37       27      -10       
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/142?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/nowide/utf/utf.hpp](https://codecov.io/gh/boostorg/nowide/pull/142/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvdXRmL3V0Zi5ocHA=) | `100.00% <ø> (+1.55%)` | :arrow_up: |  
| [src/iostream.cpp](https://codecov.io/gh/boostorg/nowide/pull/142/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2lvc3RyZWFtLmNwcA==) | `79.41% <ø> (+2.48%)` | :arrow_up: |  
| [test/test\_iostream.cpp](https://codecov.io/gh/boostorg/nowide/pull/142/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2lvc3RyZWFtLmNwcA==) | `100.00% <ø> (+0.47%)` | :arrow_up: |  
| [include/boost/nowide/utf8\_codecvt.hpp](https://codecov.io/gh/boostorg/nowide/pull/142/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvdXRmOF9jb2RlY3Z0LmhwcA==) | `100.00% <100.00%> (+1.20%)` | :arrow_up: |  
| [test/test\_codecvt.cpp](https://codecov.io/gh/boostorg/nowide/pull/142/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVjdnQuY3Bw) | `100.00% <100.00%> (+0.46%)` | :arrow_up: |  
| [include/boost/nowide/args.hpp](https://codecov.io/gh/boostorg/nowide/pull/142/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9ub3dpZGUvYXJncy5ocHA=) | `95.83% <0.00%> (-0.09%)` | :arrow_down: |  
| [test/test\_fs.cpp](https://codecov.io/gh/boostorg/nowide/pull/142/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2ZzLmNwcA==) | `100.00% <0.00%> (ø)` | |  
| [test/test\_env.cpp](https://codecov.io/gh/boostorg/nowide/pull/142/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Vudi5jcHA=) | `100.00% <0.00%> (ø)` | |  
| [test/test\_sets.hpp](https://codecov.io/gh/boostorg/nowide/pull/142/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3NldHMuaHBw) | `100.00% <0.00%> (ø)` | |  
| [test/test\_stat.cpp](https://codecov.io/gh/boostorg/nowide/pull/142/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N0YXQuY3Bw) | `100.00% <0.00%> (ø)` | |  
| ... and [20 more](https://codecov.io/gh/boostorg/nowide/pull/142/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |

---
