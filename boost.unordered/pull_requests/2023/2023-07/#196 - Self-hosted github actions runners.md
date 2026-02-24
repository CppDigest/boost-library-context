# #196 Self-hosted github actions runners [Merged]

> Username: sdarwin  
> Created at: 2023-07-11 18:11:03 UTC  
> Updated at: 2023-07-13 08:12:44 UTC  
> Merged at: 2023-07-13 08:12:44 UTC  
> Closed at: 2023-07-13 08:12:44 UTC  
> Url: https://github.com/boostorg/unordered/pull/196  

GitHub Actions jobs may run on AWS when CI is slow. Documentation https://github.com/cppalliance/githubactions

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-07-11 23:36:25 UTC  
> Updated_at: 2023-07-13 00:38:41 UTC  
> Url: https://github.com/boostorg/unordered/pull/196#issuecomment-1631642076  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/196?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#196](https://app.codecov.io/gh/boostorg/unordered/pull/196?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (97b5287) into [develop](https://app.codecov.io/gh/boostorg/unordered/commit/9a7d1d336aaa73ad8e5f7c07bdb81b2e793f8d93?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9a7d1d3) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/196/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/196?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #196   +/-   ##  
========================================  
  Coverage    97.87%   97.87%             
========================================  
  Files          128      128             
  Lines        18642    18642             
========================================  
  Hits         18245    18245             
  Misses         397      397             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/196?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/196?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9a7d1d3...97b5287](https://app.codecov.io/gh/boostorg/unordered/pull/196?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: joaquintides  
> Created_at: 2023-07-12 16:41:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/196#issuecomment-1632870856  

Hi @sdarwin, thanks for this contribution! Looks like the dedicated server times out for some tests?

---

## Comment 3

> Username: sdarwin  
> Created_at: 2023-07-12 16:53:25 UTC  
> Url: https://github.com/boostorg/unordered/pull/196#issuecomment-1632886572  

> server times out for some tests?  
  
That is certainly interesting. One job failed after 3 hours and 30 minutes.   I have now switched the runners from "spot" instances (which can be reclaimed by AWS) to "on-demand" instances (billed per second, and not "spot").    I will push a small change to the PR and retest.

---

## Comment 4

> Username: sdarwin  
> Created_at: 2023-07-12 20:45:01 UTC  
> Url: https://github.com/boostorg/unordered/pull/196#issuecomment-1633191765  

And, again.    
A timeout in the ci.yml file is what's causing the cancellation.

---
