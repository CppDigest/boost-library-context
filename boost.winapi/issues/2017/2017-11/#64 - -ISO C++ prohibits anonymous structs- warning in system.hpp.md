# #64 - "ISO C++ prohibits anonymous structs" warning in system.hpp [Closed]

> Username: pdimov  
> Created at: 2017-11-27 16:46:47 UTC  
> Updated at: 2017-11-27 19:45:02 UTC  
> Closed at: 2017-11-27 19:45:02 UTC  
> Url: https://github.com/boostorg/winapi/issues/64  

The Pool tests use `-Werror` and fail under MinGW with  
  
```  
..\../boost/winapi/system.hpp:52:9: error: ISO C++ prohibits anonymous structs [-Werror=pedantic]  
         } DUMMYSTRUCTNAME;  
         ^  
```  
  
The member is given a dummy name, but the `struct` itself isn't.

---

## Comment 1

> Username: Lastique  
> Created at: 2017-11-27 19:45:02 UTC  
> Url: https://github.com/boostorg/winapi/issues/64#issuecomment-347303661  

Duplicates https://github.com/boostorg/winapi/issues/62.
