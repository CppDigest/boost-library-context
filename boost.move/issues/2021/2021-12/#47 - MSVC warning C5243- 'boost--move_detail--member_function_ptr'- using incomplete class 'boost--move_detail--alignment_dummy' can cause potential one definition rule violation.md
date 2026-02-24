# #47 - MSVC warning C5243: 'boost::move_detail::member_function_ptr': using incomplete class 'boost::move_detail::alignment_dummy' can cause potential one definition rule violation due to ABI limitation [Closed]

> Username: Kojoley  
> Created at: 2021-12-15 23:40:03 UTC  
> Updated at: 2023-04-18 20:41:13 UTC  
> Closed at: 2021-12-26 21:25:10 UTC  
> Url: https://github.com/boostorg/move/issues/47  

```  
boost/move/detail/type_traits.hpp(1128): warning C5243: 'boost::move_detail::member_function_ptr': using incomplete class 'boost::move_detail::alignment_dummy' can cause potential one definition rule violation due to ABI limitation  
boost/move/detail/type_traits.hpp(1102): note: see declaration of 'boost::move_detail::alignment_dummy'  
boost/move/detail/type_traits.hpp(1128): note: consider using inheritance keywords on the forward declaration of the class to avoid the issue: 'class [__single_inheritance|__multiple_inheritance|__virtual_inheritance] class-name;'  
```  
https://godbolt.org/z/5xEaKfYMn

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-12-26 21:25:34 UTC  
> Url: https://github.com/boostorg/move/issues/47#issuecomment-1001243749  

Many thanks for the report!

---

## Comment 2

> Username: Kojoley  
> Created at: 2021-12-26 21:28:35 UTC  
> Url: https://github.com/boostorg/move/issues/47#issuecomment-1001244025  

Thanks for fixing the warnings!

---

## Comment 3

> Username: soroush  
> Created at: 2023-04-18 07:40:14 UTC  
> Url: https://github.com/boostorg/move/issues/47#issuecomment-1512603948  

@igaztanaga Thanks for the fix  
Do you know of any workaround that doesn't involve updating the boost to the new version?

---

## Comment 4

> Username: igaztanaga  
> Created at: 2023-04-18 20:41:12 UTC  
> Url: https://github.com/boostorg/move/issues/47#issuecomment-1513769512  

I can see any workaround other than apply the patch to your local version:  
  
https://github.com/boostorg/move/commit/dc9edc458cf1936511fc2e503469045e4d43ac31
