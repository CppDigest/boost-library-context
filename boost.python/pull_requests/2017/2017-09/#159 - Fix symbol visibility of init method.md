# #159 Fix symbol visibility of init method [Merged]

> Username: marv  
> Created at: 2017-09-20 17:22:26 UTC  
> Updated at: 2017-10-25 00:19:01 UTC  
> Merged at: 2017-10-25 00:19:00 UTC  
> Closed at: 2017-10-25 00:19:00 UTC  
> Url: https://github.com/boostorg/python/pull/159  

This was properly an oversight when switching to the BOOST_SYMBOL_*  
symbols from Boost.Config in commit 0224f54a (see #1)  
  
Since Boost.Config is already doing the differentiation between  
different platforms and compilers we can simplify this bit and just  
use BOOST_SYMBOL_EXPORT  
  
This should fix #131

---

## Comment 1

> Username: micbou  
> Created_at: 2017-09-30 08:37:36 UTC  
> Url: https://github.com/boostorg/python/pull/159#issuecomment-333293885  

I can confirm this fix the visibility issue. Without this change, we have to downgrade Boost to 1.63.0 to use the `-fvisibility=hidden` flag in our project. See PR https://github.com/Valloric/ycmd/pull/848.

---
