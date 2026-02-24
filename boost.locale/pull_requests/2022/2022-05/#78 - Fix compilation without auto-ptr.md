# #78 Fix compilation without auto-ptr [Closed]

> Username: Flamefire  
> Created at: 2022-05-17 14:14:14 UTC  
> Updated at: 2022-06-03 09:00:15 UTC  
> Closed at: 2022-05-24 13:25:10 UTC  
> Url: https://github.com/boostorg/locale/pull/78  

Unify the 2 defines `BOOST_LOCALE_HIDE_AUTO_PTR` & `BOOST_NO_AUTO_PTR` to a new `BOOST_LOCALE_USE_AUTO_PTR` then use this consistently.  
  
Especially test/test_codepage_converter.cpp was missing any guarding and failed to compile.  
  
Requires:  
  
- [ ] #76  
- [ ] #77

---
