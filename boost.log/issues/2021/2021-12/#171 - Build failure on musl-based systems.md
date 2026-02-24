# #171 - Build failure on musl-based systems [Closed]

> Username: leonardohn  
> Created at: 2021-12-13 01:30:58 UTC  
> Updated at: 2021-12-13 04:14:51 UTC  
> Closed at: 2021-12-13 04:14:51 UTC  
> Url: https://github.com/boostorg/log/issues/171  

Boost 1.78.0 is failing to build on the Gentoo linux distribution with musl libc, with the following error message:  
  
```  
In file included from libs/log/src/permissions.cpp:18:  
./boost/interprocess/permissions.hpp:73:14: error: no type named 'mode_t' in the global namespace  
   typedef ::mode_t    os_permissions_type;  
           ~~^  
1 error generated.  
```  
  
It seems that `sys/stat.h` needs to be included somewhere.

---

## Comment 1

> Username: leonardohn  
> Created at: 2021-12-13 04:14:51 UTC  
> Url: https://github.com/boostorg/log/issues/171#issuecomment-992094810  

Problem fixed with https://github.com/boostorg/interprocess/pull/162.
