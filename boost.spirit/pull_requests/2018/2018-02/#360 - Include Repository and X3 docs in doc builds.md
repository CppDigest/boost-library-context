# #360 Include Repository and X3 docs in doc builds [Merged]

> Username: Kojoley  
> Created at: 2018-02-15 16:28:40 UTC  
> Updated at: 2018-02-17 23:55:02 UTC  
> Merged at: 2018-02-17 23:54:59 UTC  
> Closed at: 2018-02-17 23:54:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/360  

For some reason boost stopped building docs for 'sublibs' in 1.62. X3 docs were never integrated into build   
 system.  
  
Also includes some fixed to Repository doc build errors and versions info adjustments.  
  
Closes #357, [trac 12322](https://svn.boost.org/trac10/ticket/12322).

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2018-02-17 14:03:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/360#pullrequestreview-97374943  

📁 doc/spirit2.qbk

```diff
 462 |+ __spirit__ (currently, __version__). For the newest (C++14) version, please
 463 |+ follow to __x3__. If you're looking for the documentation of Spirit's first
 464 |+ generation version (formerly Spirit V1.8), see __classic__.
```

> Username: Kojoley  
> Created_at: 2018-02-17 14:03:58 UTC  
> Updated_at: 2018-02-17 14:03:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/360#discussion_r168922522  

@djowel Do you agree with the wording?

> Username: djowel  
> Created_at: 2018-02-17 23:17:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/360#discussion_r168935524  

> @djowel Do you agree with the wording?  
  
LGTM


---
