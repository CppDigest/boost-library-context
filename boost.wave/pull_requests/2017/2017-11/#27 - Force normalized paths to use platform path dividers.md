# #27 Force normalized paths to use platform path dividers [Merged]

> Username: jefftrull  
> Created at: 2017-11-30 06:43:44 UTC  
> Updated at: 2017-11-30 13:43:37 UTC  
> Merged at: 2017-11-30 13:43:37 UTC  
> Closed at: 2017-11-30 13:43:37 UTC  
> Url: https://github.com/boostorg/wave/pull/27  

Wave calls `boost::filesystem::normalize()`, which unfortunately can switch to forward slashes for the first (root) component of the path. Path objects have a method `make_preferred()` to force the use of the platform's preferred dividers - backslash, in the case of Windows.  This makes the unit test output comparisons pass (in particular, the three that were failing in MSVC builds).

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2017-11-30 13:40:48 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/27#pullrequestreview-80183683  

Nice catch! Thanks!

---
