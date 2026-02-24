# #136 Specify C++11 requirement in top-level Jamfile [Merged]

> Username: jefftrull  
> Created at: 2021-12-09 18:43:44 UTC  
> Updated at: 2021-12-09 22:34:52 UTC  
> Merged at: 2021-12-09 22:34:52 UTC  
> Closed at: 2021-12-09 22:34:52 UTC  
> Url: https://github.com/boostorg/wave/pull/136  

Removing C++03 from Wave's CI config isn't enough - the top-level Boost  
build will try to build Wave under unsupported standards/compiler versions  
without an explicit requirement in the Jamfile.

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2021-12-09 19:03:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/136#pullrequestreview-828084742  

LGTM, thanks!

---
