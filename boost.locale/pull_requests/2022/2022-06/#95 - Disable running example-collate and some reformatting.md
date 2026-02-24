# #95 Disable running example/collate and some reformatting [Merged]

> Username: Flamefire  
> Created at: 2022-06-29 12:55:55 UTC  
> Updated at: 2022-06-30 16:47:40 UTC  
> Merged at: 2022-06-30 13:08:09 UTC  
> Closed at: 2022-06-30 13:08:09 UTC  
> Url: https://github.com/boostorg/locale/pull/95  

That example reads from stdin causing timeouts in some envs.  
  
Remove `using namespace std;` to differentiate between Boost.Locale and `std` and adjust related formatting.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-30 07:02:29 UTC  
> Updated_at: 2022-06-30 09:00:58 UTC  
> Url: https://github.com/boostorg/locale/pull/95#issuecomment-1170844496  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/95?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#95](https://codecov.io/gh/boostorg/locale/pull/95?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a07e058) into [develop](https://codecov.io/gh/boostorg/locale/commit/50105d8d46c8875afba97c8ecf728aa03c0ac18f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (50105d8) will **increase** coverage by `0.98%`.  
> The diff coverage is `88.27%`.  
  
> :exclamation: Current head a07e058 differs from pull request most recent head 75f3a98. Consider uploading reports for the commit 75f3a98 to get more accurate results  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #95      +/-   ##  
===========================================  
+ Coverage    79.92%   80.91%   +0.98%       
===========================================  
  Files           76       75       -1       
  Lines         5944     5900      -44       
===========================================  
+ Hits          4751     4774      +23       
+ Misses        1193     1126      -67       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/95?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/generic\_codecvt.hpp](https://codecov.io/gh/boostorg/locale/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZ2VuZXJpY19jb2RlY3Z0LmhwcA==) | `97.14% <ø> (ø)` | |  
| [src/shared/date\_time.cpp](https://codecov.io/gh/boostorg/locale/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3NoYXJlZC9kYXRlX3RpbWUuY3Bw) | `67.94% <33.33%> (ø)` | |  
| [test/test\_std\_formatting.cpp](https://codecov.io/gh/boostorg/locale/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N0ZF9mb3JtYXR0aW5nLmNwcA==) | `76.72% <50.00%> (+1.42%)` | :arrow_up: |  
| [test/test\_posix\_formatting.cpp](https://codecov.io/gh/boostorg/locale/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3Bvc2l4X2Zvcm1hdHRpbmcuY3Bw) | `78.57% <55.00%> (+5.90%)` | :arrow_up: |  
| [test/test\_posix\_convert.cpp](https://codecov.io/gh/boostorg/locale/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3Bvc2l4X2NvbnZlcnQuY3Bw) | `75.60% <66.66%> (+12.34%)` | :arrow_up: |  
| [test/test\_codepage.cpp](https://codecov.io/gh/boostorg/locale/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVwYWdlLmNwcA==) | `88.25% <89.39%> (+2.82%)` | :arrow_up: |  
| [test/test\_message.cpp](https://codecov.io/gh/boostorg/locale/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X21lc3NhZ2UuY3Bw) | `96.53% <92.50%> (+0.99%)` | :arrow_up: |  
| [test/test\_locale.hpp](https://codecov.io/gh/boostorg/locale/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2xvY2FsZS5ocHA=) | `95.55% <92.85%> (+11.18%)` | :arrow_up: |  
| [test/test\_date\_time.cpp](https://codecov.io/gh/boostorg/locale/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2RhdGVfdGltZS5jcHA=) | `95.32% <95.83%> (+1.64%)` | :arrow_up: |  
| [test/test\_codepage\_converter.cpp](https://codecov.io/gh/boostorg/locale/pull/95/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVwYWdlX2NvbnZlcnRlci5jcHA=) | `99.38% <99.21%> (+1.79%)` | :arrow_up: |  
| ... and [35 more](https://codecov.io/gh/boostorg/locale/pull/95/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/95?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/95?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [50105d8...75f3a98](https://codecov.io/gh/boostorg/locale/pull/95?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
