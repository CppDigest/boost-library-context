# #177 Using a common variable for program files [Merged]

> Username: teeks99  
> Created at: 2017-03-15 11:58:37 UTC  
> Updated at: 2021-10-02 22:18:50 UTC  
> Merged at: 2017-03-15 15:02:14 UTC  
> Closed at: 2017-03-15 15:02:14 UTC  
> Url: https://github.com/boostorg/build/pull/177  

On 32-bit windows computers, visual studio is installed to %ProgramFiles% and  
the %ProgramFiles(x86)% variable is not defined (I was mistaken in an earlier  
issue thread). On 64-bit windows computers, visual studio is installed to  
%ProgramFiles(x86)%. This commit uses the %VS_ProgramFiles% variable to search  
the appropriate one.

---
