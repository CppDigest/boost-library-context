# #234 - Potential non consistency in documentation [Closed]

> Username: denzor200  
> Created at: 2021-09-25 17:50:58 UTC  
> Updated at: 2021-09-26 01:02:04 UTC  
> Closed at: 2021-09-26 01:02:04 UTC  
> Url: https://github.com/boostorg/fusion/issues/234  

https://www.boost.org/doc/libs/1_77_0/libs/fusion/doc/html/fusion/container/generation/functions/tiers.html  
This documentation said that a const reference cannot be as an element of some tier.  
However, this examples compiles successfully:  
https://godbolt.org/z/9b3jh48xv

---

## Comment 1

> Username: djowel  
> Created at: 2021-09-26 00:22:26 UTC  
> Url: https://github.com/boostorg/fusion/issues/234#issuecomment-927203036  

Your code is correct and allowed. The documentation is misleading and should be fixed.

---

## Comment 2

> Username: djowel  
> Created at: 2021-09-26 01:01:27 UTC  
> Url: https://github.com/boostorg/fusion/issues/234#issuecomment-927206138  

Fixed in develop branch
