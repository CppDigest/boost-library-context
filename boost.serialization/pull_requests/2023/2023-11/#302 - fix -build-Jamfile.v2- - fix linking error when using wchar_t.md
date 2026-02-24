# #302 fix "build/Jamfile.v2" - fix linking error when using wchar_t [Closed]

> Username: gh-user-2022  
> Created at: 2023-11-29 10:00:47 UTC  
> Updated at: 2024-01-16 16:00:04 UTC  
> Closed at: 2024-01-09 20:25:16 UTC  
> Url: https://github.com/boostorg/serialization/pull/302  

I can provide the exact linker error and/or reproducible example, if necessary.  
  
(This is a very old bug, present in boost-1.69 (and possibly before), I had to patch the boost for several years.)

---

## Comment 1

> Username: robertramey  
> Created_at: 2024-01-09 05:37:25 UTC  
> Url: https://github.com/boostorg/serialization/pull/302#issuecomment-1882442031  

I have a question about this.  
  
The b2 file contains:   
SOURCES =    ...  
   codecvt_null  
  
which means that this code is included in the library "boost_serialization".  Hence it should not be necessary in the library boost_wserialization. So it seems to me that making this change would mean the code is included twice.  Perhaps the code IS in boost_serialization but there might be some issue with visibility setting which prevents the linking to the code in boost_serialization.  Or ... I don't know.  Needs more investigation.

---

## Comment 2

> Username: gh-user-2022  
> Created_at: 2024-01-16 15:58:08 UTC  
> Url: https://github.com/boostorg/serialization/pull/302#issuecomment-1894034290  

There is following error without this patch:  
  
```  
[build] ld: ......./libboost_wserialization-gcc112-mt-d-1_69.a(basic_text_wiprimitive.o): in function `boost::archive::codecvt_null<wchar_t>::codecvt_null(unsigned long)':  
[build] ......./boost/archive/codecvt_null.hpp:97: undefined reference to `vtable for boost::archive::codecvt_null<wchar_t>'  
```

---

## Comment 3

> Username: gh-user-2022  
> Created_at: 2024-01-16 16:00:03 UTC  
> Url: https://github.com/boostorg/serialization/pull/302#issuecomment-1894037781  

And apparently there was already a patch for this problem, but it was rejected: https://github.com/boostorg/serialization/pull/42

---
