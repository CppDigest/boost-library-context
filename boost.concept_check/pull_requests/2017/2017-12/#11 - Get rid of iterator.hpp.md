# #11 Get rid of iterator.hpp [Merged]

> Username: DanielaE  
> Created at: 2017-12-27 08:35:11 UTC  
> Updated at: 2018-01-03 05:52:19 UTC  
> Merged at: 2017-12-29 04:27:41 UTC  
> Closed at: 2017-12-29 04:27:41 UTC  
> Url: https://github.com/boostorg/concept_check/pull/11  

It does nothing more than pulling 'std::iterator' into namespace boost and including standard libraries 'iterator' and 'cstddef'. This library only takes advantage of the included headers. OTOH, pulling 'std::iterator' into boost generates deprecation warnings in MSVC 14.1 when compiling in C++17 mode. Besides that, Boost's 'iterator.hpp' is deprecated, too.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: mclow  
> Created_at: 2017-12-27 14:55:24 UTC  
> Url: https://github.com/boostorg/concept_check/pull/11#issuecomment-354125131  

This looks fine to me.

---

## Comment 2

> Username: eldiener  
> Created_at: 2017-12-27 23:26:24 UTC  
> Updated_at: 2017-12-27 23:27:18 UTC  
> Url: https://github.com/boostorg/concept_check/pull/11#issuecomment-354195322  

I wonder why <boost/iterator.hpp> was ever included as opposed to just \<iterator\>. Does anyone know the reason for this ?

---
