# #30 Change archives to backup and restore the C++ locale: [Closed]

> Username: jzmaddock  
> Created at: 2015-10-17 18:19:48 UTC  
> Updated at: 2017-05-23 22:49:59 UTC  
> Closed at: 2017-05-23 22:49:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/30  

avoids double delete of the locale's facets.  
Move utf8_codecvt_facet.cpp into main library as it's referenced from there.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-10-17 18:21:23 UTC  
> Url: https://github.com/boostorg/serialization/pull/30#issuecomment-148939190  

Note: this PR does not completely fix the msvc tests - there are unresolved externals when building the dll version of the library.  However, building with link=static passes most tests now (a couple of those also fail with unresolved externals).

---

## Comment 2

> Username: robertramey  
> Created_at: 2017-05-23 22:49:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/30#issuecomment-303554656  

I think this is fixed.  There are some changes susequent to these so it's hard to check

---
