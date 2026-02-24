# #37 fix a static initialization fiasco with ios_base [Merged]

> Username: nevion  
> Created at: 2015-08-16 12:41:39 UTC  
> Updated at: 2015-10-17 03:59:18 UTC  
> Merged at: 2015-10-17 03:59:18 UTC  
> Closed at: 2015-10-17 03:59:18 UTC  
> Url: https://github.com/boostorg/python/pull/37  

see http://stackoverflow.com/questions/12318693/c-segmentation-fault-  
when-using-cout-in-static-variable-initialization for a reference of  
what was happening when iostreams were used within boost.python  
registry's global static ctors.

---
