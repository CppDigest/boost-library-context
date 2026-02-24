# #133 - Test `template_constructor.cpp` doesn't work since MSVC 19.20 [Closed]

> Username: denzor200  
> Created at: 2023-07-01 18:25:46 UTC  
> Updated at: 2025-06-20 14:44:08 UTC  
> Closed at: 2025-06-20 14:44:08 UTC  
> Url: https://github.com/boostorg/pfr/issues/133  

https://godbolt.org/z/E8Pb1dzP3  
It works in MSVC 19.16

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-06-20 14:44:08 UTC  
> Url: https://github.com/boostorg/pfr/issues/133#issuecomment-2991897378  

Looks like it has been fixed in 19.29 or even earlier https://regression.boost.io/develop/developer/pfr.html
