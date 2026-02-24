# #118 Simplify `ios_prop` and test [Merged]

> Username: Flamefire  
> Created at: 2022-10-11 17:41:52 UTC  
> Updated at: 2022-10-12 21:07:04 UTC  
> Merged at: 2022-10-12 21:07:01 UTC  
> Closed at: 2022-10-12 21:07:01 UTC  
> Url: https://github.com/boostorg/locale/pull/118  

Only the `get()` method is required, the rest can be removed. Also rewrite the test to better explain behavior

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-10-12 03:01:11 UTC  
> Updated_at: 2022-10-12 17:52:02 UTC  
> Url: https://github.com/boostorg/locale/pull/118#issuecomment-1275520030  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/118?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#118](https://codecov.io/gh/boostorg/locale/pull/118?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8487701) into [develop](https://codecov.io/gh/boostorg/locale/commit/b8a1a66de35029c96ff7ac0809539541fcb44e04?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b8a1a66) will **increase** coverage by `1.21%`.  
> The diff coverage is `96.61%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/118/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/118?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #118      +/-   ##  
===========================================  
+ Coverage    86.98%   88.19%   +1.21%       
===========================================  
  Files           93      100       +7       
  Lines         7989     8894     +905       
===========================================  
+ Hits          6949     7844     +895       
- Misses        1040     1050      +10       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/118?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/shared/ios\_prop.hpp](https://codecov.io/gh/boostorg/locale/pull/118/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvaW9zX3Byb3AuaHBw) | `92.59% <88.88%> (+6.87%)` | :arrow_up: |  
| [test/test\_ios\_prop.cpp](https://codecov.io/gh/boostorg/locale/pull/118/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2lvc19wcm9wLmNwcA==) | `100.00% <100.00%> (+6.81%)` | :arrow_up: |  
| [include/boost/locale/localization\_backend.hpp](https://codecov.io/gh/boostorg/locale/pull/118/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbG9jYWxpemF0aW9uX2JhY2tlbmQuaHBw) | `75.00% <0.00%> (-25.00%)` | :arrow_down: |  
| [src/boost/locale/util/default\_locale.cpp](https://codecov.io/gh/boostorg/locale/pull/118/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2RlZmF1bHRfbG9jYWxlLmNwcA==) | `67.85% <0.00%> (-23.06%)` | :arrow_down: |  
| [test/test\_formatting.cpp](https://codecov.io/gh/boostorg/locale/pull/118/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zvcm1hdHRpbmcuY3Bw) | `92.30% <0.00%> (-7.70%)` | :arrow_down: |  
| [src/boost/locale/shared/formatting.cpp](https://codecov.io/gh/boostorg/locale/pull/118/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvZm9ybWF0dGluZy5jcHA=) | `87.17% <0.00%> (-7.63%)` | :arrow_down: |  
| [include/boost/locale/generic\_codecvt.hpp](https://codecov.io/gh/boostorg/locale/pull/118/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZ2VuZXJpY19jb2RlY3Z0LmhwcA==) | `95.00% <0.00%> (-5.00%)` | :arrow_down: |  
| [src/boost/locale/util/timezone.hpp](https://codecov.io/gh/boostorg/locale/pull/118/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL3RpbWV6b25lLmhwcA==) | `87.50% <0.00%> (-3.81%)` | :arrow_down: |  
| [test/test\_posix\_formatting.cpp](https://codecov.io/gh/boostorg/locale/pull/118/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3Bvc2l4X2Zvcm1hdHRpbmcuY3Bw) | `95.95% <0.00%> (-1.94%)` | :arrow_down: |  
| [test/test\_date\_time.cpp](https://codecov.io/gh/boostorg/locale/pull/118/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2RhdGVfdGltZS5jcHA=) | `98.40% <0.00%> (-1.60%)` | :arrow_down: |  
| ... and [41 more](https://codecov.io/gh/boostorg/locale/pull/118/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/118?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/118?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b8a1a66...8487701](https://codecov.io/gh/boostorg/locale/pull/118?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
