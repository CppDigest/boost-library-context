# #17 - Warning [Closed]

> Username: vinniefalco  
> Created at: 2022-08-11 18:16:38 UTC  
> Updated at: 2022-12-15 17:33:53 UTC  
> Closed at: 2022-12-15 17:33:53 UTC  
> Url: https://github.com/boostorg/quickbook/issues/17  

Here:  
https://github.com/boostorg/quickbook/blob/5f2e8c24cb813b4b0b94d3316e664b971c5b71dd/src/parsers.hpp#L81  
  
Warning:  
```  
\quickbook\src\parsers.hpp(81): warning C4709: comma operator within array index expression  
```  
  
Compiling with VS2019

---

## Comment 1

> Username: Kojoley  
> Created at: 2022-09-23 23:33:35 UTC  
> Url: https://github.com/boostorg/quickbook/issues/17#issuecomment-1256796425  

This warning is broken in MSVC for ages https://developercommunity.visualstudio.com/t/buggy-warning-c4709/471956

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-26 18:16:44 UTC  
> Url: https://github.com/boostorg/quickbook/issues/17#issuecomment-1258428941  

Can we work around it or something, to stop it from warning?

---

## Comment 3

> Username: Kojoley  
> Created at: 2022-09-27 23:19:00 UTC  
> Url: https://github.com/boostorg/quickbook/issues/17#issuecomment-1260171222  

> Can we work around it or something, to stop it from warning?  
  
Please check #20 whether it helps or not.
