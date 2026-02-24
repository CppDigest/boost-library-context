# #67 Remove unused ios_info::data [Closed]

> Username: alxrmorozov  
> Created at: 2021-09-17 00:33:12 UTC  
> Updated at: 2021-09-17 13:07:09 UTC  
> Closed at: 2021-09-17 13:06:53 UTC  
> Url: https://github.com/boostorg/locale/pull/67  



---

## Comment 1

> Username: artyom-beilis  
> Created_at: 2021-09-17 03:53:42 UTC  
> Url: https://github.com/boostorg/locale/pull/67#issuecomment-921434330  

No. It is designated for future binary compatability.   
  
Google Qt d-poonter/pimple

---

## Comment 2

> Username: artyom-beilis  
> Created_at: 2021-09-17 03:57:01 UTC  
> Url: https://github.com/boostorg/locale/pull/67#issuecomment-921435318  

Boost.locale designed for binary compatibility. Is is also part of cppcms project under booster namesapace. Same code as it.  
  
This means if there a change or bug in the code that requires a new member it is added into d pointer

---

## Comment 3

> Username: alxrmorozov  
> Created_at: 2021-09-17 13:06:53 UTC  
> Url: https://github.com/boostorg/locale/pull/67#issuecomment-921782066  

Thank you for your clarification

---
