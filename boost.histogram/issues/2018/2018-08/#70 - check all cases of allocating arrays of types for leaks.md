# #70 - check all cases of allocating arrays of types for leaks [Closed]

> Username: HDembinski  
> Created at: 2018-08-28 22:14:29 UTC  
> Updated at: 2018-09-02 14:41:01 UTC  
> Closed at: 2018-09-02 14:40:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/70  

Glen pointed out that an exception thrown while constructing arrays of types will leak the already constructed instances and the memory that was allocated.  
  
> Your loop needs to catch, destroy what you've already constructed, and rethrow the exception.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-09-02 14:41:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/70#issuecomment-417935466  

Fixed in develop
