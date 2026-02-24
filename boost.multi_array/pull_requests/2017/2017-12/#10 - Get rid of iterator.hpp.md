# #10 Get rid of iterator.hpp [Merged]

> Username: DanielaE  
> Created at: 2017-12-27 15:02:09 UTC  
> Updated at: 2018-10-14 00:43:39 UTC  
> Merged at: 2018-10-14 00:43:39 UTC  
> Closed at: 2018-10-14 00:43:39 UTC  
> Url: https://github.com/boostorg/multi_array/pull/10  

It does nothing more than pulling std::iterator into namespace boost and including headers. This library only takes advantage of the included headers. OTOH, pulling std::iterator into boost generates deprecation warnings in MSVC 14.1 when compiling in C++17 mode. Even more, Boost's iterator.hpp is deprecated, too.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Review 1 [Approved]

> Username: glenfe  
> Created_at: 2018-09-09 00:49:58 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/multi_array/pull/10#pullrequestreview-153568303  

Looks good to me.

---
