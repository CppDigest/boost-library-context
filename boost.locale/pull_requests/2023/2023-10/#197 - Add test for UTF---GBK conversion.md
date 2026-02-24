# #197 Add test for UTF<->GBK conversion [Merged]

> Username: Flamefire  
> Created at: 2023-10-10 09:48:41 UTC  
> Updated at: 2023-10-12 17:51:56 UTC  
> Merged at: 2023-10-12 17:51:53 UTC  
> Closed at: 2023-10-12 17:51:53 UTC  
> Url: https://github.com/boostorg/locale/pull/197  

Test for #196, should pass, especially without crashing.  
  
Result is based on observed values verified via https://www.fileformat.info/info/charset/GBK/list.htm  
  
> … | [HORIZONTAL ELLIPSIS (U+2026)](https://www.fileformat.info/info/unicode/char/2026/index.htm) | a1ad

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-10-10 15:01:13 UTC  
> Updated_at: 2023-10-12 17:28:35 UTC  
> Url: https://github.com/boostorg/locale/pull/197#issuecomment-1755638650  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/197?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#197](https://app.codecov.io/gh/boostorg/locale/pull/197?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (624f836) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/d97969a0cb5e12e20b6b568de53d5895b2054aa6?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d97969a) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/197/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/197?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #197   +/-   ##  
========================================  
  Coverage    95.26%   95.26%             
========================================  
  Files          114      114             
  Lines        10001    10002    +1       
========================================  
+ Hits          9527     9528    +1       
  Misses         474      474             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/locale/pull/197?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_encoding.cpp](https://app.codecov.io/gh/boostorg/locale/pull/197?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2VuY29kaW5nLmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/197?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/197?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d97969a...624f836](https://app.codecov.io/gh/boostorg/locale/pull/197?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
