# #815 refactor: move detail headers to src [Merged]

> Username: alandefreitas  
> Created at: 2024-01-19 15:27:32 UTC  
> Updated at: 2024-02-21 00:56:07 UTC  
> Merged at: 2024-01-19 18:34:31 UTC  
> Closed at: 2024-01-19 18:34:31 UTC  
> Url: https://github.com/boostorg/url/pull/815  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-01-19 15:51:35 UTC  
> Updated_at: 2024-01-19 15:54:42 UTC  
> Url: https://github.com/boostorg/url/pull/815#issuecomment-1900667027  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`fcb5f58`)](https://app.codecov.io/gh/boostorg/url/commit/fcb5f58b0ebe64646841bffe3c52304c46a2fa71?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 97.26% compared to head [(`f595ebe`)](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 97.24%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/815/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #815      +/-   ##  
===========================================  
- Coverage    97.26%   97.24%   -0.03%       
===========================================  
  Files          156      157       +1       
  Lines         8587     8587                
===========================================  
- Hits          8352     8350       -2       
- Misses         235      237       +2       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/vformat.hpp](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3Zmb3JtYXQuaHBw) | `100.00% <ø> (ø)` | |  
| [src/authority\_view.cpp](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2F1dGhvcml0eV92aWV3LmNwcA==) | `85.11% <ø> (ø)` | |  
| [src/detail/any\_params\_iter.cpp](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9hbnlfcGFyYW1zX2l0ZXIuY3Bw) | `99.44% <ø> (ø)` | |  
| [src/detail/any\_segments\_iter.cpp](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9hbnlfc2VnbWVudHNfaXRlci5jcHA=) | `100.00% <ø> (ø)` | |  
| [src/detail/decode.cpp](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9kZWNvZGUuY3Bw) | `85.96% <ø> (ø)` | |  
| [src/detail/format\_args.cpp](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9mb3JtYXRfYXJncy5jcHA=) | `100.00% <ø> (ø)` | |  
| [src/detail/move\_chars.hpp](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9tb3ZlX2NoYXJzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [src/detail/normalize.cpp](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9ub3JtYWxpemUuY3Bw) | `91.35% <ø> (-0.47%)` | :arrow_down: |  
| [src/detail/normalize.hpp](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9ub3JtYWxpemUuaHBw) | `100.00% <ø> (ø)` | |  
| [src/detail/over\_allocator.hpp](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9vdmVyX2FsbG9jYXRvci5ocHA=) | `100.00% <ø> (ø)` | |  
| ... and [33 more](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [fcb5f58...f595ebe](https://app.codecov.io/gh/boostorg/url/pull/815?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-01-19 15:52:31 UTC  
> Url: https://github.com/boostorg/url/pull/815#issuecomment-1900668481  

GCOVR code coverage report [https://815.url.prtest2.cppalliance.org/gcovr/index.html](https://815.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://815.url.prtest2.cppalliance.org/genhtml/index.html](https://815.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://815.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://815.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
