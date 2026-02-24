# #169 - error: friend declaration with a constraint that depends on an enclosing template parameter must be a definition [Closed]

> Username: intractabilis  
> Created at: 2024-04-24 18:48:20 UTC  
> Updated at: 2024-09-29 20:47:30 UTC  
> Closed at: 2024-09-29 19:51:17 UTC  
> Url: https://github.com/boostorg/parser/issues/169  

https://github.com/tzlaine/parser/blob/4cea9c03d6baf8165a21162e66be4f99ec85b529/include/boost/parser/detail/text/transcode_view.hpp#L178  
Clang 18.1.4 with `-std=gnu++23` complains here that this friend function must be defined, not just declared. It seems correct according to [13.7.5 Friends](https://eel.is/c++draft/temp.friend#9).

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-09-29 20:43:25 UTC  
> Url: https://github.com/boostorg/parser/issues/169#issuecomment-2381596087  

Thanks for reporting this!

---

## Comment 2

> Username: intractabilis  
> Created at: 2024-09-29 20:47:29 UTC  
> Url: https://github.com/boostorg/parser/issues/169#issuecomment-2381597202  

My pleasure. Great job with this library!
