# #415 build boost_url_extra [Merged]

> Username: alandefreitas  
> Created at: 2022-08-16 23:37:19 UTC  
> Updated at: 2022-08-30 21:04:08 UTC  
> Merged at: 2022-08-17 00:15:10 UTC  
> Closed at: 2022-08-17 00:15:10 UTC  
> Url: https://github.com/boostorg/url/pull/415  

The PR fixes the GHA tests that broke at daf605409a694217b96c27ebc3d33a1a76b8f82c  
  
It's a single line of code.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2022-08-16 23:37:58 UTC  
> Url: https://github.com/boostorg/url/pull/415#issuecomment-1217276539  

That kinda sucks that the CI script has to know the targets

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-08-16 23:52:31 UTC  
> Updated_at: 2022-08-16 23:53:11 UTC  
> Url: https://github.com/boostorg/url/pull/415#issuecomment-1217285297  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/415?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#415](https://codecov.io/gh/CPPAlliance/url/pull/415?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (2aca736) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/daf605409a694217b96c27ebc3d33a1a76b8f82c?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (daf6054) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/415/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/415?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #415   +/-   ##  
========================================  
  Coverage    97.89%   97.89%             
========================================  
  Files          132      132             
  Lines         6281     6281             
========================================  
  Hits          6149     6149             
  Misses         132      132             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/415?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/415?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [daf6054...2aca736](https://codecov.io/gh/CPPAlliance/url/pull/415?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2022-08-16 23:59:25 UTC  
> Url: https://github.com/boostorg/url/pull/415#issuecomment-1217289015  

Yes. That's because the script doesn't build everything there is. That's useful to save some time.

---
