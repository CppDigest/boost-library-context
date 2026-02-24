# #135 The library implementations in MSVC 14.0 and 14.1 have both version n… [Merged]

> Username: DanielaE  
> Created at: 2017-05-19 14:09:16 UTC  
> Updated at: 2017-05-20 11:33:10 UTC  
> Merged at: 2017-05-20 10:27:23 UTC  
> Closed at: 2017-05-20 10:27:23 UTC  
> Url: https://github.com/boostorg/config/pull/135  

…umber V6.50:0009, and therefore _CPPLIB_VER is defined to 650. But the library in 14.0 does *not* have std::apply whereas the one in 14.1 *does*.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: glenfe  
> Created_at: 2017-05-19 16:31:42 UTC  
> Updated_at: 2017-05-19 16:33:00 UTC  
> Url: https://github.com/boostorg/config/pull/135#issuecomment-302750500  

This is really unfortunate that _CPPLIB_VER is not incremented in 14.1. We will need a good (simple) way to disambiguate MSVC standard library versions going forward.

---

## Comment 2

> Username: DanielaE  
> Created_at: 2017-05-19 17:05:24 UTC  
> Url: https://github.com/boostorg/config/pull/135#issuecomment-302758260  

Yeah, but I really hope this is just a one-time fluke.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2017-05-19 18:50:04 UTC  
> Url: https://github.com/boostorg/config/pull/135#issuecomment-302782469  

Just so you all know, I'm trying to get the travis test matrix clean   
before I merge anything new...  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---
