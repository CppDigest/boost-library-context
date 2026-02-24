# #72 - The sizeof(*) should not be multiplied in the first parameter of memset [Closed]

> Username: CreateRan  
> Created at: 2024-11-21 12:39:16 UTC  
> Updated at: 2024-11-21 17:08:20 UTC  
> Closed at: 2024-11-21 17:07:53 UTC  
> Url: https://github.com/boostorg/atomic/issues/72  

In L1255 and L1259 of file lock_pool.cpp,  the sizeof(*) should not be multiplied in the first parameter of memset  
  
Pointer arithmetic in C and C++ is automatically scaled according to the size of the data type. For example, if the type of p is T* and sizeof(T) == 4 then the expression p+1 adds 4 bytes to p.  
  
This query finds code of the form p + k*sizeof(T). Such code is usually a mistake because there is no need to manually scale the offset by sizeof(T).

---

## Comment 1

> Username: Lastique  
> Created at: 2024-11-21 17:08:19 UTC  
> Url: https://github.com/boostorg/atomic/issues/72#issuecomment-2491809866  

Thank you for the report, should be fixed now.
