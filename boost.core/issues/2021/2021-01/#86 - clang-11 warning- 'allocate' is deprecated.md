# #86 - clang-11 warning: 'allocate' is deprecated [Closed]

> Username: k15tfu  
> Created at: 2021-01-28 15:46:23 UTC  
> Updated at: 2021-02-16 03:15:24 UTC  
> Closed at: 2021-02-16 03:15:24 UTC  
> Url: https://github.com/boostorg/core/issues/86  

In file boost/core/allocator_access.hpp:  
```  
boost/core/allocator_access.hpp:349:14: error: 'allocate' is deprecated [-Werror,-Wdeprecated-declarations]  
    return a.allocate(n, h);  
             ^  
1 error generated.  
```  
  
Linked issue: #75

---

## Comment 1

> Username: glenfe  
> Created at: 2021-02-16 03:15:24 UTC  
> Url: https://github.com/boostorg/core/issues/86#issuecomment-779553494  

Should be resolved in 86bff4c2d381c1832b6ff61fabe90635ff91d9fc, thanks.
