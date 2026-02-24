# #7 Variable name hz conflicts with the definition of an AIX system variable [Closed]

> Username: ismirlian  
> Created at: 2014-07-16 21:33:07 UTC  
> Updated at: 2014-07-30 14:42:33 UTC  
> Closed at: 2014-07-30 14:42:33 UTC  
> Url: https://github.com/boostorg/interprocess/pull/7  

Having a variable named hz conflicts with the definition of an AIX system variable that is in scope with this file. If I undefine the variable hz, hz can be safely defined without any confusion.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2014-07-30 14:42:33 UTC  
> Url: https://github.com/boostorg/interprocess/pull/7#issuecomment-50623979  

Renamed the variable to "ticks_per_second" in  
  
[develop 3c09cee] Renamed variable "hz" as it conflicts with AIX's system variable.

---
