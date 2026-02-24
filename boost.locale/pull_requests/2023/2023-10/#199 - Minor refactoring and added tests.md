# #199 Minor refactoring and added tests [Merged]

> Username: Flamefire  
> Created at: 2023-10-13 10:03:25 UTC  
> Updated at: 2023-10-14 06:37:49 UTC  
> Merged at: 2023-10-14 06:35:42 UTC  
> Closed at: 2023-10-14 06:35:42 UTC  
> Url: https://github.com/boostorg/locale/pull/199  

Fix a warning ("0-as-nullptr") and add missing tests  
  
Also some minor refactoring for clearer code.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-10-13 22:09:30 UTC  
> Url: https://github.com/boostorg/locale/pull/199#issuecomment-1762303794  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/199?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#199](https://app.codecov.io/gh/boostorg/locale/pull/199?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c67a5f2) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/3c647f2f7fc63fd619b386d24aeb3279e7597d36?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3c647f2) will **increase** coverage by `0.40%`.  
> The diff coverage is `99.35%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/199/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/199?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #199      +/-   ##  
===========================================  
+ Coverage    95.26%   95.66%   +0.40%       
===========================================  
  Files          114      114                
  Lines        10002    10131     +129       
===========================================  
+ Hits          9528     9692     +164       
+ Misses         474      439      -35       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/locale/pull/199?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/date\_time.hpp](https://app.codecov.io/gh/boostorg/locale/pull/199?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZGF0ZV90aW1lLmhwcA==) | `100.00% <100.00%> (+4.96%)` | :arrow_up: |  
| [include/boost/locale/formatting.hpp](https://app.codecov.io/gh/boostorg/locale/pull/199?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZm9ybWF0dGluZy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/locale/hold\_ptr.hpp](https://app.codecov.io/gh/boostorg/locale/pull/199?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvaG9sZF9wdHIuaHBw) | `100.00% <100.00%> (+5.55%)` | :arrow_up: |  
| [include/boost/locale/util.hpp](https://app.codecov.io/gh/boostorg/locale/pull/199?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRpbC5ocHA=) | `100.00% <ø> (+90.47%)` | :arrow_up: |  
| [src/boost/locale/icu/codecvt.cpp](https://app.codecov.io/gh/boostorg/locale/pull/199?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvY29kZWN2dC5jcHA=) | `84.09% <100.00%> (+4.92%)` | :arrow_up: |  
| [test/test\_codecvt.cpp](https://app.codecov.io/gh/boostorg/locale/pull/199?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVjdnQuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_date\_time.cpp](https://app.codecov.io/gh/boostorg/locale/pull/199?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2RhdGVfdGltZS5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [test/test\_util.cpp](https://app.codecov.io/gh/boostorg/locale/pull/199?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3V0aWwuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_boundary.cpp](https://app.codecov.io/gh/boostorg/locale/pull/199?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2JvdW5kYXJ5LmNwcA==) | `97.82% <0.00%> (ø)` | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/199/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/199?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/199?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3c647f2...c67a5f2](https://app.codecov.io/gh/boostorg/locale/pull/199?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
