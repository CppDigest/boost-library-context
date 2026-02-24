# #9 Fix compilation error in GNU gettext backend [Closed]

> Username: Lastique  
> Created at: 2015-05-31 21:46:59 UTC  
> Updated at: 2018-08-12 08:32:33 UTC  
> Closed at: 2018-08-12 08:32:33 UTC  
> Url: https://github.com/boostorg/locale/pull/9  

Eventually, hash_function gets instantiated on wchar_t by create_messages_facet<wchar_t>(). The pointer conversion has to be a reinterpret_cast instead of static_cast.

---

## Comment 1

> Username: BenWiederhake  
> Created_at: 2015-09-20 11:56:14 UTC  
> Url: https://github.com/boostorg/locale/pull/9#issuecomment-141781457  

How can I help to get this into develop? I'd like to see the compilation error fixed.

---

## Comment 2

> Username: artyom-beilis  
> Created_at: 2018-08-12 08:32:33 UTC  
> Url: https://github.com/boostorg/locale/pull/9#issuecomment-412327535  

Already fixed

---
