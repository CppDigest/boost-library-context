# #282 Fix example in README.md [Merged]

> Username: akrzemi1  
> Created at: 2022-07-31 08:33:36 UTC  
> Updated at: 2022-07-31 15:05:40 UTC  
> Merged at: 2022-07-31 15:05:40 UTC  
> Closed at: 2022-07-31 15:05:40 UTC  
> Url: https://github.com/boostorg/url/pull/282  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-07-31 11:55:45 UTC  
> Url: https://github.com/boostorg/url/pull/282#issuecomment-1200409840  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/282?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#282](https://codecov.io/gh/CPPAlliance/url/pull/282?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (3209cac) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/5ffeeadb6b6d06975a66cff1316046359ff589ff?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (5ffeead) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/282/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/282?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #282   +/-   ##  
========================================  
  Coverage    96.63%   96.63%             
========================================  
  Files          113      113             
  Lines         5978     5978             
========================================  
  Hits          5777     5777             
  Misses         201      201             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/282?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/282?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [5ffeead...3209cac](https://codecov.io/gh/CPPAlliance/url/pull/282?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-31 12:20:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/282#pullrequestreview-1056559764  

📁 README.md

```diff
  73 |  .remove_query()
  74 |-  .remove_fragment();
  74 |+  .remove_fragment()
```

> Username: vinniefalco  
> Created_at: 2022-07-31 12:20:26 UTC  
> Url: https://github.com/boostorg/url/pull/282#discussion_r933978250  

some habits are hard to break :)


---
