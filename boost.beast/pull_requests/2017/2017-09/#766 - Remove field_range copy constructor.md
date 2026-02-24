# #766 Remove field_range copy constructor [Closed]

> Username: HowardHinnant  
> Created at: 2017-09-05 17:17:27 UTC  
> Updated at: 2017-09-10 03:11:25 UTC  
> Closed at: 2017-09-10 03:11:25 UTC  
> Url: https://github.com/boostorg/beast/pull/766  

*  This presence of the user-declared copy constructor makes  
   the compiler-provided copy assignment operator deprecated.  
   This change allows the compiler to provide both copy members  
   without deprecated behavior.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-09-10 03:11:25 UTC  
> Url: https://github.com/boostorg/beast/pull/766#issuecomment-328316683  

Merged in version 113

---
