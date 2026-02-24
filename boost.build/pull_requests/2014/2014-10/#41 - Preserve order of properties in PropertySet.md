# #41 Preserve order of properties in PropertySet [Closed]

> Username: frenchtoast747  
> Created at: 2014-10-06 18:57:57 UTC  
> Updated at: 2021-10-02 22:35:39 UTC  
> Closed at: 2014-10-24 16:54:22 UTC  
> Url: https://github.com/boostorg/build/pull/41  

From what I've been told, the order of includes and defines (from BoostBuild's perspective) should not matter and if it does, the `<include>a&&b` mechanism should be used.  
  
This change was created in the spirit of preserving backwards compatibility and causes the command output in Python to match the command output in Jam.  
  
**Note**: I've tested this change using the `timeit` module and preserving the order can cause construction of the new list to be an **order of magnitude slower**, however, on small property sets, this will still be negligible.

---

## Comment 1

> Username: vprus  
> Created_at: 2014-10-24 07:13:33 UTC  
> Url: https://github.com/boostorg/build/pull/41#issuecomment-60352264  

Aaron, what exactly did you find to be order of magnitude slower? Actual creation of property sets when using stable sort, on a testsuite creating many property sets with a lot of properties? Or is that slowdown for actual workloads?

---

## Comment 2

> Username: frenchtoast747  
> Created_at: 2014-10-24 15:34:04 UTC  
> Url: https://github.com/boostorg/build/pull/41#issuecomment-60404159  

The slowdown is only for the single call of  `unique(x, stable=True)` vs `unique(x)`. Relative to the rest of the build, the slowdown is insignificant.

---

## Comment 3

> Username: vprus  
> Created_at: 2014-10-24 16:54:22 UTC  
> Url: https://github.com/boostorg/build/pull/41#issuecomment-60415972  

Thanks, that's quite expected. I've merged the change, thanks!

---
