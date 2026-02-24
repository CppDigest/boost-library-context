# #60 Fix error_category destructor [Closed]

> Username: Mr-Leshiy  
> Created at: 2021-01-19 12:54:13 UTC  
> Updated at: 2021-01-21 18:11:58 UTC  
> Closed at: 2021-01-21 18:11:54 UTC  
> Url: https://github.com/boostorg/system/pull/60  

destruct of the error_category that is an abstract class, should be virtual for the correct inheritance of this class.  
Unfortunately it is not possible to use this class as base class in projects with an enabled compile warning -Werror=non-virtual-dtor, where all compiler warnings are treated as errors.

---
