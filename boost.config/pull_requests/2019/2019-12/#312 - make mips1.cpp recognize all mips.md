# #312 make mips1.cpp recognize all mips [Merged]

> Username: wzssyqa  
> Created at: 2019-12-18 11:46:44 UTC  
> Updated at: 2020-01-25 11:09:55 UTC  
> Merged at: 2020-01-25 11:09:55 UTC  
> Closed at: 2020-01-25 11:09:55 UTC  
> Url: https://github.com/boostorg/config/pull/312  

currently mips1.cpp can only recognize mips 32bit (MIPS1 by __mips=1,  
and mips2 above by R3000).  
  
64bit cannot be recognized.  
So drop the __mips==1 here.

---
