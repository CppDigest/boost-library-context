# #28 Use BOOST_NO_FENV_H to detect whether <fenv.h> is supported (fix #24). [Merged]

> Username: silene  
> Created at: 2021-02-03 17:25:03 UTC  
> Updated at: 2021-04-04 03:58:19 UTC  
> Merged at: 2021-04-04 03:58:19 UTC  
> Closed at: 2021-04-04 03:58:19 UTC  
> Url: https://github.com/boostorg/interval/pull/28  

This should fix compilation on platforms that provide <fenv.h> but do not set __USE_ISOC99.

---

## Comment 1

> Username: lrineau  
> Created_at: 2021-03-29 12:55:05 UTC  
> Url: https://github.com/boostorg/interval/pull/28#issuecomment-809353792  

What is the status of this pull-request? It is needed to compile Interval on Apple M1 (an ARM64 CPU).

---
