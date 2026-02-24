# #703 make_error_code is constexpr [Merged]

> Username: alandefreitas  
> Created at: 2023-03-02 16:18:25 UTC  
> Updated at: 2023-07-24 14:36:32 UTC  
> Merged at: 2023-03-02 22:53:22 UTC  
> Closed at: 2023-03-02 22:53:22 UTC  
> Url: https://github.com/boostorg/url/pull/703  

This replicates how http-proto does error codes:   
  
https://github.com/CPPAlliance/http_proto/commit/dc7d2730b8d5cc5fba1d4900459eb9467289bdfc

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-03-02 16:35:36 UTC  
> Url: https://github.com/boostorg/url/pull/703#issuecomment-1452170418  

GCOVR code coverage report [https://703.url.prtest.cppalliance.org/gcovr/index.html](https://703.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://703.url.prtest.cppalliance.org/genhtml/index.html](https://703.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://703.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://703.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-03-02 16:35:38 UTC  
> Updated_at: 2023-03-02 22:35:30 UTC  
> Url: https://github.com/boostorg/url/pull/703#issuecomment-1452170479  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/703?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#703](https://codecov.io/gh/boostorg/url/pull/703?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1e6011e) into [develop](https://codecov.io/gh/boostorg/url/commit/cc4e01e2941f5f44a0a437cb0a461e157681b418?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (cc4e01e) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/703/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/703?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #703   +/-   ##  
========================================  
  Coverage    97.12%   97.12%             
========================================  
  Files          154      154             
  Lines         8432     8432             
========================================  
  Hits          8190     8190             
  Misses         242      242             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/703?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/error.ipp](https://codecov.io/gh/boostorg/url/pull/703?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9lcnJvci5pcHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/error.hpp](https://codecov.io/gh/boostorg/url/pull/703?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9lcnJvci5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/703?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/703?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [cc4e01e...1e6011e](https://codecov.io/gh/boostorg/url/pull/703?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-03-02 22:29:47 UTC  
> Url: https://github.com/boostorg/url/pull/703#issuecomment-1452647837  

GCOVR code coverage report [https://703.url.prtest.cppalliance.org/gcovr/index.html](https://703.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://703.url.prtest.cppalliance.org/genhtml/index.html](https://703.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://703.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://703.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
