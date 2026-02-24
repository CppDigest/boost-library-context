# #252 - dispatch is deprecated [Closed]

> Username: vinniefalco  
> Created at: 2022-08-14 23:09:35 UTC  
> Updated at: 2022-08-15 10:39:59 UTC  
> Closed at: 2022-08-15 10:39:59 UTC  
> Url: https://github.com/boostorg/filesystem/issues/252  

But filesystem still uses it:  
https://github.com/boostorg/filesystem/blob/bb7dc550d5274ccb76e774c2b996333977de4347/include/boost/filesystem/path.hpp#L619

---

## Comment 1

> Username: Lastique  
> Created at: 2022-08-15 10:39:59 UTC  
> Url: https://github.com/boostorg/filesystem/issues/252#issuecomment-1214873635  

Only certain `dispatch` overloads are deprecated, others are not. If you're getting a deprecated warning then you are passing an argument that is not a string or a C-style string, and *that* is deprecated. Also, I replied on the ML.
