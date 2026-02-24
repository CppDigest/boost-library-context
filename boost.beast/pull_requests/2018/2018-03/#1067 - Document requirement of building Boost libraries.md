# #1067 Document requirement of building Boost libraries [Closed]

> Username: mloskot  
> Created at: 2018-03-11 20:20:07 UTC  
> Updated at: 2018-03-20 21:42:01 UTC  
> Closed at: 2018-03-12 15:04:18 UTC  
> Url: https://github.com/boostorg/beast/pull/1067  

This should also clarify the CMake configuration provided  
does not build any of Beast's dependencies within Boost.  
  
Closes #1064

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2018-03-11 20:22:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1067#pullrequestreview-102894935  

📁 README.md

```diff
 114 |- necessary to first obtain the boost "superproject" along with all
 115 |- of the boost libraries. Instructions for doing so may be found on
 114 |+ necessary to first obtain the boost "superproject" along with sources of
```

> Username: vinniefalco  
> Created_at: 2018-03-11 20:22:51 UTC  
> Updated_at: 2018-03-11 20:49:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1067#discussion_r173668077  

Shouldn't "boost" be capitalized?

> Username: mloskot  
> Created_at: 2018-03-11 20:49:36 UTC  
> Url: https://github.com/boostorg/beast/pull/1067#discussion_r173668970  

Yes, indeed, thx. I've updated the PR.


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2018-03-11 22:59:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1067#issuecomment-372157548  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1067?src=pr&el=h1) Report  
> Merging [#1067](https://codecov.io/gh/boostorg/beast/pull/1067?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/6a34afb38a6a957bca2f2f970e067aa258e802b5?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1067/graphs/tree.svg?height=150&width=650&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1067?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1067   +/-   ##  
========================================  
  Coverage    95.51%   95.51%             
========================================  
  Files          106      106             
  Lines        10627    10627             
========================================  
  Hits         10150    10150             
  Misses         477      477  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1067?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1067?src=pr&el=footer). Last update [6a34afb...09a4d3e](https://codecov.io/gh/boostorg/beast/pull/1067?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-03-12 15:03:50 UTC  
> Url: https://github.com/boostorg/beast/pull/1067#issuecomment-372341418  

@vinniefalco & @djarek Thanks for the review and accepting the PR.  
  
It looks the change has made it into the develop branch in https://github.com/boostorg/beast/commit/06ee7c7ac62641c78ddc3abc771b9d1eca3bd8ad  
Interestingly, the PR has not been closed automatically. Am I supposed to do it?

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2018-03-12 15:04:18 UTC  
> Url: https://github.com/boostorg/beast/pull/1067#issuecomment-372341585  

Merged to 165, thanks!

---
