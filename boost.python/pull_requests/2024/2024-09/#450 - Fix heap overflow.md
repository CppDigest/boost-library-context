# #450 Fix heap overflow [Closed]

> Username: andrew-rogers-sndk  
> Created at: 2024-09-04 11:25:47 UTC  
> Updated at: 2024-10-02 10:21:09 UTC  
> Closed at: 2024-09-22 16:32:37 UTC  
> Url: https://github.com/boostorg/python/pull/450  

* Quell size warning:  
  ```  
  libs\python\src\object\class.cpp(732): warning C4267: 'initializing': conversion from 'size_t' to 'int', possible loss of data  
  ```  
* Fix heap overflow (#449).

---

## Comment 1

> Username: andrew-rogers-sndk  
> Created_at: 2024-10-02 10:21:08 UTC  
> Url: https://github.com/boostorg/python/pull/450#issuecomment-2388295165  

This fix was incorporated via #392.

---
