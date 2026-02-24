# #654 refactor segments compare function [Merged]

> Username: alandefreitas  
> Created at: 2023-01-10 20:00:18 UTC  
> Updated at: 2023-07-24 14:36:55 UTC  
> Merged at: 2023-01-19 01:24:13 UTC  
> Closed at: 2023-01-19 01:24:13 UTC  
> Url: https://github.com/boostorg/url/pull/654  

fix #653

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-01-10 20:16:07 UTC  
> Url: https://github.com/boostorg/url/pull/654#issuecomment-1377795440  

GCOVR code coverage report [https://654.url.prtest.cppalliance.org/gcovr/index.html](https://654.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://654.url.prtest.cppalliance.org/genhtml/index.html](https://654.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://654.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://654.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-01-10 22:35:38 UTC  
> Updated_at: 2023-01-11 17:56:04 UTC  
> Url: https://github.com/boostorg/url/pull/654#issuecomment-1377985132  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/654?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#654](https://codecov.io/gh/boostorg/url/pull/654?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (97a5d3d) into [develop](https://codecov.io/gh/boostorg/url/commit/af8aa0388769fb2d9c44a9ef8919acd215739df3?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (af8aa03) will **increase** coverage by `0.02%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/654/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/654?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #654      +/-   ##  
===========================================  
+ Coverage    96.88%   96.90%   +0.02%       
===========================================  
  Files          147      147                
  Lines         7924     7925       +1       
===========================================  
+ Hits          7677     7680       +3       
+ Misses         247      245       -2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/654?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/impl/normalize.ipp](https://codecov.io/gh/boostorg/url/pull/654?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvbm9ybWFsaXplLmlwcA==) | `91.37% <100.00%> (+0.51%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/654?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/654?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [af8aa03...97a5d3d](https://codecov.io/gh/boostorg/url/pull/654?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-01-11 17:17:48 UTC  
> Url: https://github.com/boostorg/url/pull/654#issuecomment-1379213903  

GCOVR code coverage report [https://654.url.prtest.cppalliance.org/gcovr/index.html](https://654.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://654.url.prtest.cppalliance.org/genhtml/index.html](https://654.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://654.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://654.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2023-01-11 19:32:45 UTC  
> Url: https://github.com/boostorg/url/pull/654#issuecomment-1379384307  

@klemens-morgenstern any other failing cases?

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-01-12 22:34:04 UTC  
> Url: https://github.com/boostorg/url/pull/654#issuecomment-1381068468  

GCOVR code coverage report [https://654.url.prtest.cppalliance.org/gcovr/index.html](https://654.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://654.url.prtest.cppalliance.org/genhtml/index.html](https://654.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://654.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://654.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
