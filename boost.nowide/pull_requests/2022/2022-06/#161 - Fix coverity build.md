# #161 Fix coverity build [Merged]

> Username: Flamefire  
> Created at: 2022-06-14 10:39:10 UTC  
> Updated at: 2022-06-14 15:15:50 UTC  
> Merged at: 2022-06-14 15:15:46 UTC  
> Closed at: 2022-06-14 15:15:47 UTC  
> Url: https://github.com/boostorg/nowide/pull/161  

- `cov-configure --msvc` is required to configure coverity for MSVC  
- `PublishCoverity` seems to be so outdated that manual commands (zipping via 7z then upload via curl) work better  
- Fix and add a few supressions

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-14 13:37:26 UTC  
> Updated_at: 2022-06-14 13:41:11 UTC  
> Url: https://github.com/boostorg/nowide/pull/161#issuecomment-1155199005  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/161?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#161](https://codecov.io/gh/boostorg/nowide/pull/161?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (993deff) into [develop](https://codecov.io/gh/boostorg/nowide/commit/8cb8207c928e5c47fa35256cc9eaf43c2d3b9534?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8cb8207) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #161   +/-   ##  
========================================  
  Coverage    99.46%   99.46%             
========================================  
  Files           33       33             
  Lines         3383     3385    +2       
========================================  
+ Hits          3365     3367    +2       
  Misses          18       18             
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/161?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_codecvt.cpp](https://codecov.io/gh/boostorg/nowide/pull/161/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVjdnQuY3Bw) | `100.00% <ø> (ø)` | |  
| [test/test\_convert.cpp](https://codecov.io/gh/boostorg/nowide/pull/161/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbnZlcnQuY3Bw) | `100.00% <ø> (ø)` | |  
| [test/test\_env.cpp](https://codecov.io/gh/boostorg/nowide/pull/161/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Vudi5jcHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_filebuf.cpp](https://codecov.io/gh/boostorg/nowide/pull/161/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2ZpbGVidWYuY3Bw) | `100.00% <ø> (ø)` | |  
| [test/test\_fs.cpp](https://codecov.io/gh/boostorg/nowide/pull/161/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2ZzLmNwcA==) | `100.00% <ø> (ø)` | |  
| [test/test\_fstream.cpp](https://codecov.io/gh/boostorg/nowide/pull/161/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2ZzdHJlYW0uY3Bw) | `100.00% <ø> (ø)` | |  
| [test/test\_fstream\_special.cpp](https://codecov.io/gh/boostorg/nowide/pull/161/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2ZzdHJlYW1fc3BlY2lhbC5jcHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_ifstream.cpp](https://codecov.io/gh/boostorg/nowide/pull/161/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2lmc3RyZWFtLmNwcA==) | `100.00% <ø> (ø)` | |  
| [test/test\_iostream.cpp](https://codecov.io/gh/boostorg/nowide/pull/161/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2lvc3RyZWFtLmNwcA==) | `100.00% <ø> (ø)` | |  
| [test/test\_ofstream.cpp](https://codecov.io/gh/boostorg/nowide/pull/161/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X29mc3RyZWFtLmNwcA==) | `100.00% <ø> (ø)` | |  
| ... and [6 more](https://codecov.io/gh/boostorg/nowide/pull/161/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |

---
