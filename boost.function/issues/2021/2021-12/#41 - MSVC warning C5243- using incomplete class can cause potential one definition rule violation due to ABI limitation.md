# #41 - MSVC warning C5243: using incomplete class can cause potential one definition rule violation due to ABI limitation [Closed]

> Username: Kojoley  
> Created at: 2021-12-16 00:17:10 UTC  
> Updated at: 2021-12-16 02:50:03 UTC  
> Closed at: 2021-12-16 02:50:03 UTC  
> Url: https://github.com/boostorg/function/issues/41  

```  
boost/function/function_base.hpp(91): warning C5243: 'void (__cdecl boost::detail::function::X::* )(int)': using incomplete class 'boost::detail::function::X' can cause potential one definition rule violation due to ABI limitation  
```  
https://godbolt.org/z/r4cPrx3EY

---

## Comment 1

> Username: pdimov  
> Created at: 2021-12-16 00:30:04 UTC  
> Url: https://github.com/boostorg/function/issues/41#issuecomment-995325969  

5243 is disabled by default, right?

---

## Comment 2

> Username: Kojoley  
> Created at: 2021-12-16 00:35:05 UTC  
> Url: https://github.com/boostorg/function/issues/41#issuecomment-995328532  

Yes. It is a new level 1 warning, I would not be surprised if it will be enabled by default in the future.
