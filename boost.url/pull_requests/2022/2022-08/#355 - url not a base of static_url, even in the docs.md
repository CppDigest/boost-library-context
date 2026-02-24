# #355  url not a base of static_url, even in the docs [Closed]

> Username: akrzemi1  
> Created at: 2022-08-06 14:22:51 UTC  
> Updated at: 2022-08-06 16:31:40 UTC  
> Closed at: 2022-08-06 16:31:40 UTC  
> Url: https://github.com/boostorg/url/pull/355  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-06 14:27:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/355#pullrequestreview-1064312549  

📁 include/boost/url/static_url.hpp

```diff
  73 | #ifdef BOOST_URL_DOCS
  74 |-     : public url
  74 |+     : public url_base
```

> Username: vinniefalco  
> Created_at: 2022-08-06 14:27:03 UTC  
> Updated_at: 2022-08-06 14:27:04 UTC  
> Url: https://github.com/boostorg/url/pull/355#discussion_r939534806  

Yep. "That which isn't compiled, contains errors."


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-08-06 14:37:31 UTC  
> Updated_at: 2022-08-06 14:39:48 UTC  
> Url: https://github.com/boostorg/url/pull/355#issuecomment-1207225773  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/355?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#355](https://codecov.io/gh/CPPAlliance/url/pull/355?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (8f5dd46) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/3e19ab7f4e11e7ac3b0cc5b6814a6827e8f67c37?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (3e19ab7) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/355/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/355?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #355   +/-   ##  
========================================  
  Coverage    96.16%   96.16%             
========================================  
  Files          124      124             
  Lines         6161     6161             
========================================  
  Hits          5925     5925             
  Misses         236      236             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/355?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/static\_url.hpp](https://codecov.io/gh/CPPAlliance/url/pull/355/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvc3RhdGljX3VybC5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/355?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/355?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [3e19ab7...8f5dd46](https://codecov.io/gh/CPPAlliance/url/pull/355?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---
