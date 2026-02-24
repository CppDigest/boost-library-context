# #123 - not building with cmake [Closed]

> Username: Duron27  
> Created at: 2024-07-04 17:48:44 UTC  
> Updated at: 2024-07-05 00:14:39 UTC  
> Closed at: 2024-07-05 00:14:39 UTC  
> Url: https://github.com/boostorg/system/issues/123  

not sure why but libboost_system.a never gets created using cmake with clang

---

## Comment 1

> Username: pdimov  
> Created at: 2024-07-04 17:51:28 UTC  
> Url: https://github.com/boostorg/system/issues/123#issuecomment-2209405002  

System is header-only. The b2 build only creates the library for backward compatibility, it doesn't do anything when linked.

---

## Comment 2

> Username: Duron27  
> Created at: 2024-07-04 18:16:28 UTC  
> Url: https://github.com/boostorg/system/issues/123#issuecomment-2209427998  

the program I'm building is older and requires it. is there a way I can create it or fake it?

---

## Comment 3

> Username: Duron27  
> Created at: 2024-07-04 18:40:44 UTC  
> Url: https://github.com/boostorg/system/issues/123#issuecomment-2209448286  

for searchability this seems to do the trick so far  
llvm-ar rc libboost_system.a find / -name "error_code.o"
