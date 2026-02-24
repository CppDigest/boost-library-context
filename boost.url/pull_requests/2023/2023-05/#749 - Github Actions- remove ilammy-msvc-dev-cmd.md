# #749 Github Actions: remove ilammy/msvc-dev-cmd [Merged]

> Username: sdarwin  
> Created at: 2023-05-12 11:37:59 UTC  
> Updated at: 2023-05-12 18:30:02 UTC  
> Merged at: 2023-05-12 18:30:02 UTC  
> Closed at: 2023-05-12 18:30:02 UTC  
> Url: https://github.com/boostorg/url/pull/749  

@alandefreitas, a suggestion to remove the 'ilammy/msvc-dev-cmd@v1' action.       
  
The reasons are:  
  
- it's not required for the CI jobs to build.  
- b2 is smart enough to find a compiler anyway.  
- when debugging gha runners, and jobs were failing, it actually became a question: "Is the ilammy/msvc-dev-cmd action involved in this crash, or it's not?"   (The latest evidence is, probably not. But still - fewer steps is better.)

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-05-12 11:57:57 UTC  
> Url: https://github.com/boostorg/url/pull/749#issuecomment-1545629489  

GCOVR code coverage report [https://749.url.prtest.cppalliance.org/gcovr/index.html](https://749.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://749.url.prtest.cppalliance.org/genhtml/index.html](https://749.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://749.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://749.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-05-12 11:59:22 UTC  
> Updated_at: 2023-05-12 12:05:10 UTC  
> Url: https://github.com/boostorg/url/pull/749#issuecomment-1545631244  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/749?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#749](https://app.codecov.io/gh/boostorg/url/pull/749?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9702b6b) into [develop](https://app.codecov.io/gh/boostorg/url/commit/bdc61cfb3694f5a561d40df7e9d91270737deeaa?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (bdc61cf) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/749/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/749?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #749   +/-   ##  
========================================  
  Coverage    97.25%   97.25%             
========================================  
  Files          155      155             
  Lines         8517     8517             
========================================  
  Hits          8283     8283             
  Misses         234      234             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/749?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/749?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [bdc61cf...9702b6b](https://app.codecov.io/gh/boostorg/url/pull/749?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
