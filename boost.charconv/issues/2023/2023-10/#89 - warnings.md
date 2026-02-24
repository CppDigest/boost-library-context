# #89 - warnings [Closed]

> Username: gpeterhoff  
> Created at: 2023-10-23 17:37:28 UTC  
> Updated at: 2023-10-24 08:36:49 UTC  
> Closed at: 2023-10-24 08:36:49 UTC  
> Url: https://github.com/boostorg/charconv/issues/89  

Hi Matt,  
in quite a few files there are direct checks for msvc:  
```#if BOOST_MSVC ...```  
This generates warnings with other compilers. Please rewrite this to  
```#if defined(BOOST_MSVC) && (BOOST_MSVC ...)```  
  
thx  
Gero

---

## Comment 1

> Username: mborland  
> Created at: 2023-10-23 17:41:36 UTC  
> Url: https://github.com/boostorg/charconv/issues/89#issuecomment-1775699351  

What toolchain and options gives you warnings? The CI runs cleanly with -Wextra and -Werror.

---

## Comment 2

> Username: pdimov  
> Created at: 2023-10-23 17:42:12 UTC  
> Url: https://github.com/boostorg/charconv/issues/89#issuecomment-1775700359  

-Wundef

---

## Comment 3

> Username: gpeterhoff  
> Created at: 2023-10-23 17:52:04 UTC  
> Url: https://github.com/boostorg/charconv/issues/89#issuecomment-1775716678  

-Wundef  
In some other places there are warnings about -Wsign-conversion
