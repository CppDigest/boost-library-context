# #252 Fix -Wregister error on Clang in C++17 mode [Merged]

> Username: Kojoley  
> Created at: 2019-03-18 18:15:49 UTC  
> Updated at: 2019-03-21 20:40:49 UTC  
> Merged at: 2019-03-21 20:40:42 UTC  
> Closed at: 2019-03-21 20:40:42 UTC  
> Url: https://github.com/boostorg/python/pull/252  

Suppresses the warning on GCC, Clang, and MSVC.  
  
Probably it is worth cherry-picking into the master as even the release managers had the trouble with Python 2 headers.  
  
Playground: https://godbolt.org/z/ioqTlS

---

## Comment 1

> Username: Kojoley  
> Created_at: 2019-03-20 20:17:17 UTC  
> Url: https://github.com/boostorg/python/pull/252#issuecomment-475010881  

Ping

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2019-03-21 20:40:49 UTC  
> Url: https://github.com/boostorg/python/pull/252#issuecomment-475393864  

Thanks !

---
