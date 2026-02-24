# #19 Workaround for old compilers like MSVC 7.1 [Merged]

> Username: igaztanaga  
> Created at: 2015-09-14 20:03:33 UTC  
> Updated at: 2015-09-15 08:09:38 UTC  
> Merged at: 2015-09-15 07:50:14 UTC  
> Closed at: 2015-09-15 07:50:14 UTC  
> Url: https://github.com/boostorg/type_traits/pull/19  

Without the indirection, a compilation error was triggered in line 106

---

## Comment 1

> Username: eldiener  
> Created_at: 2015-09-15 04:09:03 UTC  
> Url: https://github.com/boostorg/type_traits/pull/19#issuecomment-140274541  

I thought we were trying to eliminate support for such old compilers as VC++ 7.1. That's circa 2003, about 12 years old. VC++ 8 ( Visual Studio 2005 ) I have found to be just barely supportable. I personally would not attempt to support VC++ 7.1 on down for any library I wrote. I am pretty sure also that VC++ 8 is the earliest version of VC++ which we regression test.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2015-09-15 07:04:49 UTC  
> Url: https://github.com/boostorg/type_traits/pull/19#issuecomment-140300010  

AFAIK, the minimum supported by Boost is MSVC 7.1: http://www.boost.org/users/history/version_1_55_0.html  
  
FWIW, I'm still supporting it in my libraries because it's Boost's minimum, if we want to drop support for it, at least we should discuss the removal plan. I suggest merging this simple patch and then maybe start discussing new compiler minimum changes in the list. Does this make sense?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2015-09-15 07:50:07 UTC  
> Url: https://github.com/boostorg/type_traits/pull/19#issuecomment-140311360  

As the patch is trivial I'm inclined to merge, however I haven't had access to a copy of VC7.x for a long time, and given that we have no regression testers for it, it's highly likely that support for VC7.x is broken in many other ways.  Just so you know ;)

---

## Comment 4

> Username: igaztanaga  
> Created_at: 2015-09-15 08:09:38 UTC  
> Url: https://github.com/boostorg/type_traits/pull/19#issuecomment-140315693  

Thanks. I can provide patches for MSVC 7.1. Maybe I should find an old laptop and know how to run a regression tester.

---
