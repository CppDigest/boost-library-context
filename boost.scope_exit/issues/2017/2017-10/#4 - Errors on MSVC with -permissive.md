# #4 - Errors on MSVC with /permissive- [Closed]

> Username: pdimov  
> Created at: 2017-10-31 23:12:21 UTC  
> Updated at: 2017-11-22 01:32:24 UTC  
> Closed at: 2017-11-22 01:32:24 UTC  
> Url: https://github.com/boostorg/scope_exit/issues/4  

It's been reported that this ticket:  
  
https://svn.boost.org/trac10/ticket/10793  
  
has come back to life with the introduction of `/permissive-`, a supported strict mode switch.  
  
The fix is to disable the MSVC-specific bug exploit activated at  
  
https://github.com/boostorg/scope_exit/blob/develop/include/boost/scope_exit.hpp#L64

---

## Comment 1

> Username: pdimov  
> Created at: 2017-10-31 23:13:46 UTC  
> Url: https://github.com/boostorg/scope_exit/issues/4#issuecomment-340934408  

Ah, I see that a pull request already exists, #3.
