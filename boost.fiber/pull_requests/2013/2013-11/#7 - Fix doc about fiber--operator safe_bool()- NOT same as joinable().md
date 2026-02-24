# #7 Fix doc about fiber::operator safe_bool(): NOT same as joinable(). [Merged]

> Username: nat-goodspeed  
> Created at: 2013-11-19 14:54:24 UTC  
> Updated at: 2016-06-06 11:36:33 UTC  
> Merged at: 2013-11-20 06:20:37 UTC  
> Closed at: 2013-11-20 06:20:37 UTC  
> Url: https://github.com/boostorg/fiber/pull/7  

Looking at the implementation, it seems that operator safe_bool() and its  
companion, operator!(), interrogate whether the referenced fiber has or has  
not terminated. joinable() does not.  
Add more joinable() cross-references, and cross-reference joinable(), operator  
safe_bool() and operator!().

---
