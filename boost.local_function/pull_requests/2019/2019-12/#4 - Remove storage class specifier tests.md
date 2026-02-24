# #4 Remove storage class specifier tests [Merged]

> Username: Kojoley  
> Created at: 2019-12-03 21:26:48 UTC  
> Updated at: 2020-04-29 13:11:56 UTC  
> Merged at: 2020-04-29 12:52:05 UTC  
> Closed at: 2020-04-29 12:52:05 UTC  
> Url: https://github.com/boostorg/local_function/pull/4  

They fail on every compiler except GCC in GNU++98 mode. The `auto` storage  
specifier cannot be used in function parameter list in C, while C++11 already  
reused the keyword for other usages. The `register` storage specifier is  
deprecated in C++11 and removed in C++17.

---
