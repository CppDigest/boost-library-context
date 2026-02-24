# #218 Catch OSX sonoma iconv issue [Merged]

> Username: Flamefire  
> Created at: 2024-02-08 13:33:55 UTC  
> Updated at: 2024-02-12 17:13:05 UTC  
> Merged at: 2024-02-12 17:13:02 UTC  
> Closed at: 2024-02-12 17:13:02 UTC  
> Url: https://github.com/boostorg/locale/pull/218  

E2BIG shall be returned if the output buffer is too small.  
We pass a 64 byte buffer which should be enough to do *something*  
  
However it is observed on MacOS Sonoma that iconv returns E2BIG without  
doing any progress leading to an infinite loop as the same inputs will  
be passed over and over.  
Instead raise an exception when this case is detected.  
  
Closes #206

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-11 17:00:11 UTC  
> Updated_at: 2024-02-12 00:54:58 UTC  
> Url: https://github.com/boostorg/locale/pull/218#issuecomment-1937809032  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/218?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`7da8ce3`)](https://app.codecov.io/gh/boostorg/locale/commit/7da8ce387609717242e8ad78f880e818a6de9169?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 95.72% compared to head [(`5f2a828`)](https://app.codecov.io/gh/boostorg/locale/pull/218?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 95.72%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/218/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/218?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #218   +/-   ##  
========================================  
  Coverage    95.72%   95.72%             
========================================  
  Files          116      116             
  Lines         9930     9937    +7       
========================================  
+ Hits          9505     9512    +7       
  Misses         425      425             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/locale/pull/218?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/encoding/iconv\_converter.hpp](https://app.codecov.io/gh/boostorg/locale/pull/218?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy9pY29udl9jb252ZXJ0ZXIuaHBw) | `94.64% <100.00%> (ø)` | |  
| [test/test\_encoding.cpp](https://app.codecov.io/gh/boostorg/locale/pull/218?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2VuY29kaW5nLmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/218?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/218?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7da8ce3...5f2a828](https://app.codecov.io/gh/boostorg/locale/pull/218?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
