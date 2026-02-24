# #2655 Drone caching [Merged]

> Username: sdarwin  
> Created at: 2023-03-20 19:04:03 UTC  
> Updated at: 2023-08-18 07:23:02 UTC  
> Merged at: 2023-08-18 07:23:02 UTC  
> Closed at: 2023-08-18 07:23:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2655  

@klemens-morgenstern you had asked about caching to speed up the CI process. We are working on that in boostorg/url and I have copied those scripts to beast.  
Checking the main boostorg/beast CI results from the past few days, a few tests aren't passing. The situation is better in the master branch and a bit worse in the develop branch. Likewise, this pull request has a few failing tests but they mostly match up with what's already occurring in the develop branch.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-05-12 08:39:02 UTC  
> Updated_at: 2023-05-12 09:01:51 UTC  
> Url: https://github.com/boostorg/beast/pull/2655#issuecomment-1545388107  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2655?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2655](https://app.codecov.io/gh/boostorg/beast/pull/2655?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b5d7581) into [develop](https://app.codecov.io/gh/boostorg/beast/commit/3dddb5fecf8ff64bb8b841e7b252ea2b17683030?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3dddb5f) will **decrease** coverage by `0.03%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2655/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2655?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2655      +/-   ##  
===========================================  
- Coverage    92.96%   92.94%   -0.03%       
===========================================  
  Files          177      177                
  Lines        13651    13658       +7       
===========================================  
+ Hits         12691    12694       +3       
- Misses         960      964       +4       
```  
  
  
[see 3 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2655/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2655?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2655?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3dddb5f...b5d7581](https://app.codecov.io/gh/boostorg/beast/pull/2655?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2023-05-12 10:44:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2655#issuecomment-1545546142  

Fixed Drone now, is this ready to go or experimental?

---

## Comment 3

> Username: sdarwin  
> Created_at: 2023-05-12 11:13:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2655#issuecomment-1545578965  

Not tested recently.   Yes, it was ready to go.

---
