# #206 Fix bug with count_if() on AMD [Merged]

> Username: kylelutz  
> Created at: 2014-07-31 04:41:17 UTC  
> Updated at: 2014-08-12 04:29:30 UTC  
> Merged at: 2014-08-01 03:48:00 UTC  
> Closed at: 2014-08-01 03:48:00 UTC  
> Url: https://github.com/boostorg/compute/pull/206  

This fixes an issue in which the count_if_with_reduce()  
function fails to compile because convert_ulong(bool) is  
not supported.  
  
See issue #202.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-07-31 04:43:07 UTC  
> Url: https://github.com/boostorg/compute/pull/206#issuecomment-50712026  

@f-koehler can you test this out? it should fix issue #202.

---

## Comment 2

> Username: f-koehler  
> Created_at: 2014-07-31 06:34:41 UTC  
> Url: https://github.com/boostorg/compute/pull/206#issuecomment-50718911  

Thanks for the fix. It works.

---

## Comment 3

> Username: coveralls  
> Created_at: 2014-08-01 03:41:32 UTC  
> Url: https://github.com/boostorg/compute/pull/206#issuecomment-50846125  

[![Coverage Status](https://coveralls.io/builds/1031425/badge)](https://coveralls.io/builds/1031425)  
  
Coverage increased (+0.37%) when pulling **2f948a9903e1aa954ec6985114c0a615be0c7673 on fix-count-if** into **8a11a320d3bdc3e2b560fa35c5b67e4e0958a910 on develop**.

---
