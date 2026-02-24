# #11 Fix compilation on clang in C++03 mode. [Closed]

> Username: Lastique  
> Created at: 2014-06-09 11:09:51 UTC  
> Updated at: 2014-06-12 10:45:55 UTC  
> Closed at: 2014-06-09 13:15:29 UTC  
> Url: https://github.com/boostorg/thread/pull/11  

Emulated scoped enums should be casted using boost::underlying_cast instead of C-style casts or static_casts.

---
