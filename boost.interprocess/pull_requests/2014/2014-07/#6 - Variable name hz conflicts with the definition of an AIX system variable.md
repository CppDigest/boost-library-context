# #6 Variable name hz conflicts with the definition of an AIX system variable [Closed]

> Username: ismirlian  
> Created at: 2014-07-16 21:11:42 UTC  
> Updated at: 2014-07-16 21:32:45 UTC  
> Closed at: 2014-07-16 21:32:45 UTC  
> Url: https://github.com/boostorg/interprocess/pull/6  

Having a variable named hz conflicts with the definition of an AIX system variable that is in scope with this file. If I undefine the variable hz, hz can be safely defined without any confusion.

---
