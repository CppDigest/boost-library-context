# #4 Fix MSVC warning [Merged]

> Username: MarcelRaad  
> Created at: 2014-07-09 20:50:14 UTC  
> Updated at: 2014-11-10 15:27:08 UTC  
> Merged at: 2014-11-10 15:20:03 UTC  
> Closed at: 2014-11-10 15:20:03 UTC  
> Url: https://github.com/boostorg/property_tree/pull/4  

warning C4800: 'int' : forcing value to bool 'true' or 'false' (performance warning)  
Fixed by explicitly comparing the return value of isspace to zero.

---
