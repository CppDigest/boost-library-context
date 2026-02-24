# #1106 [wkt] support tabs, newlines and code cleanup [Merged]

> Username: barendgehrels  
> Created at: 2022-12-27 13:09:40 UTC  
> Updated at: 2023-01-04 09:57:20 UTC  
> Merged at: 2023-01-04 09:57:15 UTC  
> Closed at: 2023-01-04 09:57:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1106  

This PR does the following:  
- it supports tabs and newlines in WKT (see #705, that is how I noticed it - but i/o warning we better just support it)  
- there were some todo's about EMPTY, they are now checked  
- doing that, the inconsistency of writing empty polygons (`(())` vs `()`) is removed  
- fixing that, it removes the need of specific prefixes with opening brackets  
- fixing that, the existing prefixes are now used throughout such that for example `"POINT"` is defined only once  
- fixing that, I noticed a copy of the prefixes in an old unused headerfile, the multi headerfiles are now marked as deprecated  
  
It's a small breaking change because an empty polygon is now streamed as `POLYGON()` but I think that will be no problem.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2023-01-03 09:51:25 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1106#pullrequestreview-1234301668  

Thanks!

📁 test/io/wkt/wkt.cpp

```diff
 399 | }
 401 |- 
 402 |- /*
```

> Username: vissarion  
> Created_at: 2023-01-03 09:51:02 UTC  
> Updated_at: 2023-01-03 09:51:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/1106#discussion_r1060420084  

why do you want to remove this comment? Does it make sense to update it?

> Username: barendgehrels  
> Created_at: 2023-01-04 09:49:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1106#discussion_r1061304736  

These comments were from the begin time, when we wanted to check area, length etc, with another source.  
But that is long ago. Apart from that, for WKT it does not make sense (or it should be rewritten)  
And yes, it's very outdated...


---
