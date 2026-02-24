# #420 - Boost.Python doesn't build under MSVC... [Closed]

> Username: pdimov  
> Created at: 2023-08-12 21:48:56 UTC  
> Updated at: 2023-10-13 10:54:47 UTC  
> Closed at: 2023-10-13 10:54:47 UTC  
> Url: https://github.com/boostorg/python/issues/420  

... with the following error:  
  
```  
compile-c-c++ bin.v2\libs\python\build\msvc-14.2\debug\address-model-32\link-static\python-3.7\threading-multi\errors.obj  
errors.cpp  
D:\a\boost_install\boost-root\boost/config/assert_cxx03.hpp(24): fatal error C1189: #error:  "Your compiler appears not to be fully C++03 compliant.  Detected via defect macro BOOST_NO_COMPLETE_VALUE_INITIALIZATION."  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2023-08-12 21:51:16 UTC  
> Url: https://github.com/boostorg/python/issues/420#issuecomment-1676114866  

Possibly caused by https://github.com/boostorg/conversion/commit/2598281d102932d3bdc81d48d1f2e3db49cc1872.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2023-08-13 01:56:59 UTC  
> Url: https://github.com/boostorg/python/issues/420#issuecomment-1676184504  

I don't have access to that platform / compiler so I can't investigate this myself, unfortunately. It sounds like we are actually requiring (some) C++11 features that this MSVC version doesn't support.

---

## Comment 3

> Username: Turim  
> Created at: 2023-10-13 10:34:21 UTC  
> Url: https://github.com/boostorg/python/issues/420#issuecomment-1761293012  

Please, try it out for msvc-14.3? - it builds well

---

## Comment 4

> Username: pdimov  
> Created at: 2023-10-13 10:54:47 UTC  
> Url: https://github.com/boostorg/python/issues/420#issuecomment-1761318087  

Seems to have been fixed on the Conversion side.
