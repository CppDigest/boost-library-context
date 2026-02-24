# #710 setting path bypasses edit_segments [Merged]

> Username: alandefreitas  
> Created at: 2023-03-10 00:02:52 UTC  
> Updated at: 2024-02-21 00:55:07 UTC  
> Merged at: 2023-03-10 04:23:36 UTC  
> Closed at: 2023-03-10 04:23:36 UTC  
> Url: https://github.com/boostorg/url/pull/710  

fix #709

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-03-10 00:20:02 UTC  
> Url: https://github.com/boostorg/url/pull/710#issuecomment-1463026266  

GCOVR code coverage report [https://710.url.prtest.cppalliance.org/gcovr/index.html](https://710.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://710.url.prtest.cppalliance.org/genhtml/index.html](https://710.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://710.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://710.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-03-10 01:10:16 UTC  
> Url: https://github.com/boostorg/url/pull/710#issuecomment-1463059386  

GCOVR code coverage report [https://710.url.prtest.cppalliance.org/gcovr/index.html](https://710.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://710.url.prtest.cppalliance.org/genhtml/index.html](https://710.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://710.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://710.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-03-10 01:43:19 UTC  
> Updated_at: 2023-03-10 03:26:08 UTC  
> Url: https://github.com/boostorg/url/pull/710#issuecomment-1463082819  

## [Codecov](https://codecov.io/gh/boostorg/url/pull/710?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#710](https://codecov.io/gh/boostorg/url/pull/710?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (004cb66) into [develop](https://codecov.io/gh/boostorg/url/commit/e549f40c8ba14aac07634caeb86ad45b8e01e610?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e549f40) will **decrease** coverage by `0.03%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 004cb66 differs from pull request most recent head 22f4680. Consider uploading reports for the commit 22f4680 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/710/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/710?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #710      +/-   ##  
===========================================  
- Coverage    97.13%   97.11%   -0.03%       
===========================================  
  Files          155      155                
  Lines         8457     8461       +4       
===========================================  
+ Hits          8215     8217       +2       
- Misses         242      244       +2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/710?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/any\_segments\_iter.hpp](https://codecov.io/gh/boostorg/url/pull/710?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9zZWdtZW50c19pdGVyLmhwcA==) | `98.50% <ø> (-0.05%)` | :arrow_down: |  
| [...nclude/boost/url/detail/impl/any\_segments\_iter.ipp](https://codecov.io/gh/boostorg/url/pull/710?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvYW55X3NlZ21lbnRzX2l0ZXIuaXBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_base.hpp](https://codecov.io/gh/boostorg/url/pull/710?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX2Jhc2UuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/boostorg/url/pull/710?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `99.62% <100.00%> (-0.14%)` | :arrow_down: |  
  
... and [1 file with indirect coverage changes](https://codecov.io/gh/boostorg/url/pull/710/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://codecov.io/gh/boostorg/url/pull/710?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/710?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e549f40...22f4680](https://codecov.io/gh/boostorg/url/pull/710?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-03-10 03:09:03 UTC  
> Url: https://github.com/boostorg/url/pull/710#issuecomment-1463189596  

GCOVR code coverage report [https://710.url.prtest.cppalliance.org/gcovr/index.html](https://710.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://710.url.prtest.cppalliance.org/genhtml/index.html](https://710.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://710.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://710.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-03-10 03:39:33 UTC  
> Url: https://github.com/boostorg/url/pull/710#issuecomment-1463206552  

GCOVR code coverage report [https://710.url.prtest.cppalliance.org/gcovr/index.html](https://710.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://710.url.prtest.cppalliance.org/genhtml/index.html](https://710.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://710.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://710.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
