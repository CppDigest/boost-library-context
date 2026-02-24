# #70 Update for MSVC14's _HAS_AUTO_PTR_ETC [Merged]

> Username: MarcelRaad  
> Created at: 2015-08-11 08:06:40 UTC  
> Updated at: 2015-08-18 11:19:29 UTC  
> Merged at: 2015-08-18 11:17:46 UTC  
> Closed at: 2015-08-18 11:17:46 UTC  
> Url: https://github.com/boostorg/config/pull/70  

If _HAS_AUTO_PTR_ETC is defined to 0, MSVC14's standard library implements C++17's N4190, thus removing std::auto_ptr.

---

## Comment 1

> Username: eldiener  
> Created_at: 2015-08-11 15:09:34 UTC  
> Url: https://github.com/boostorg/config/pull/70#issuecomment-129923429  

The first correction from:  
  
#if _CPPLIB_VER == 650  
  
to  
  
#if defined(_CPPLIB_VER) && (_CPPLIB_VER == 650)  
  
is unnecessary. An undefined macro is always 0 in an #if statement.

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2015-08-11 15:23:49 UTC  
> Url: https://github.com/boostorg/config/pull/70#issuecomment-129928586  

Yes it's unnecessary, but at least MSVC issues a warning (C4668) when using undefined preprocessor symbols and it's checked in all other places in this file. But I actually didn't mean to commit this as it's unrelated :-\

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2015-08-18 11:17:57 UTC  
> Url: https://github.com/boostorg/config/pull/70#issuecomment-132174945  

Thanks for this.

---
