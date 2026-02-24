# #824 refactor: source files have no header guards [Merged]

> Username: alandefreitas  
> Created at: 2024-03-02 04:20:57 UTC  
> Updated at: 2024-03-04 18:59:44 UTC  
> Merged at: 2024-03-04 18:59:44 UTC  
> Closed at: 2024-03-04 18:59:44 UTC  
> Url: https://github.com/boostorg/url/pull/824  

fix #819

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-03-02 04:39:13 UTC  
> Url: https://github.com/boostorg/url/pull/824#issuecomment-1974292253  

GCOVR code coverage report [https://824.url.prtest2.cppalliance.org/gcovr/index.html](https://824.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://824.url.prtest2.cppalliance.org/genhtml/index.html](https://824.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://824.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://824.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-03-02 04:44:03 UTC  
> Updated_at: 2024-03-02 04:47:16 UTC  
> Url: https://github.com/boostorg/url/pull/824#issuecomment-1974293237  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 97.24%. Comparing base [(`0c16da3`)](https://app.codecov.io/gh/boostorg/url/commit/0c16da334765ab713420c204b840cc8fd73017d8?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`406a078`)](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/824/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #824   +/-   ##  
========================================  
  Coverage    97.24%   97.24%             
========================================  
  Files          157      157             
  Lines         8595     8595             
========================================  
  Hits          8358     8358             
  Misses         237      237             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/authority\_view.cpp](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2F1dGhvcml0eV92aWV3LmNwcA==) | `85.11% <ø> (ø)` | |  
| [src/decode\_view.cpp](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RlY29kZV92aWV3LmNwcA==) | `100.00% <ø> (ø)` | |  
| [src/detail/any\_params\_iter.cpp](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9hbnlfcGFyYW1zX2l0ZXIuY3Bw) | `99.44% <ø> (ø)` | |  
| [src/detail/any\_segments\_iter.cpp](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9hbnlfc2VnbWVudHNfaXRlci5jcHA=) | `100.00% <ø> (ø)` | |  
| [src/detail/decode.cpp](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9kZWNvZGUuY3Bw) | `85.96% <ø> (ø)` | |  
| [src/detail/except.cpp](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9leGNlcHQuY3Bw) | `100.00% <ø> (ø)` | |  
| [src/detail/format\_args.cpp](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9mb3JtYXRfYXJncy5jcHA=) | `100.00% <ø> (ø)` | |  
| [src/detail/normalize.cpp](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9ub3JtYWxpemUuY3Bw) | `91.35% <ø> (ø)` | |  
| [src/detail/params\_iter\_impl.cpp](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9wYXJhbXNfaXRlcl9pbXBsLmNwcA==) | `97.58% <ø> (ø)` | |  
| [src/detail/pattern.cpp](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9wYXR0ZXJuLmNwcA==) | `100.00% <ø> (ø)` | |  
| ... and [56 more](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0c16da3...406a078](https://app.codecov.io/gh/boostorg/url/pull/824?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
