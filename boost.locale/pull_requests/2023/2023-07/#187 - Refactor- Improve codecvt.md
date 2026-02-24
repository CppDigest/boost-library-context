# #187 Refactor: Improve codecvt [Merged]

> Username: Flamefire  
> Created at: 2023-07-02 19:38:17 UTC  
> Updated at: 2023-07-03 20:03:28 UTC  
> Merged at: 2023-07-03 18:24:11 UTC  
> Closed at: 2023-07-03 18:24:11 UTC  
> Url: https://github.com/boostorg/locale/pull/187  

Minor refactoring regarding consistency of types and improvement of the docu  
  
No functional changes

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-07-03 12:39:01 UTC  
> Updated_at: 2023-07-03 13:10:46 UTC  
> Url: https://github.com/boostorg/locale/pull/187#issuecomment-1618184296  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/187?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#187](https://app.codecov.io/gh/boostorg/locale/pull/187?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (601480a) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/2f083637942114fdfd8e8e3699100a1f61dd61b2?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2f08363) will **increase** coverage by `0.06%`.  
> The diff coverage is `97.05%`.  
  
> :exclamation: Current head 601480a differs from pull request most recent head ca48301. Consider uploading reports for the commit ca48301 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/187/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #187      +/-   ##  
===========================================  
+ Coverage    94.66%   94.72%   +0.06%       
===========================================  
  Files          113      113                
  Lines         9851     9847       -4       
===========================================  
+ Hits          9325     9328       +3       
+ Misses         526      519       -7       
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/locale/pull/187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/utf.hpp](https://app.codecov.io/gh/boostorg/locale/pull/187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRmLmhwcA==) | `98.72% <ø> (ø)` | |  
| [include/boost/locale/util.hpp](https://app.codecov.io/gh/boostorg/locale/pull/187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRpbC5ocHA=) | `9.52% <0.00%> (ø)` | |  
| [include/boost/locale/generic\_codecvt.hpp](https://app.codecov.io/gh/boostorg/locale/pull/187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZ2VuZXJpY19jb2RlY3Z0LmhwcA==) | `96.36% <100.00%> (+2.83%)` | :arrow_up: |  
| [include/boost/locale/utf8\_codecvt.hpp](https://app.codecov.io/gh/boostorg/locale/pull/187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRmOF9jb2RlY3Z0LmhwcA==) | `94.44% <100.00%> (+0.32%)` | :arrow_up: |  
| [src/boost/locale/icu/codecvt.cpp](https://app.codecov.io/gh/boostorg/locale/pull/187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvY29kZWN2dC5jcHA=) | `79.16% <100.00%> (ø)` | |  
| [src/boost/locale/shared/date\_time.cpp](https://app.codecov.io/gh/boostorg/locale/pull/187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvZGF0ZV90aW1lLmNwcA==) | `86.63% <100.00%> (-0.19%)` | :arrow_down: |  
| [src/boost/locale/shared/iconv\_codecvt.cpp](https://app.codecov.io/gh/boostorg/locale/pull/187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvaWNvbnZfY29kZWN2dC5jcHA=) | `89.87% <100.00%> (ø)` | |  
| [src/boost/locale/util/codecvt\_converter.cpp](https://app.codecov.io/gh/boostorg/locale/pull/187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2NvZGVjdnRfY29udmVydGVyLmNwcA==) | `94.07% <100.00%> (ø)` | |  
| [test/test\_codecvt.cpp](https://app.codecov.io/gh/boostorg/locale/pull/187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVjdnQuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_codepage\_converter.cpp](https://app.codecov.io/gh/boostorg/locale/pull/187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVwYWdlX2NvbnZlcnRlci5jcHA=) | `100.00% <100.00%> (ø)` | |  
  
... and [6 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/187/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/187?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/187?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2f08363...ca48301](https://app.codecov.io/gh/boostorg/locale/pull/187?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
