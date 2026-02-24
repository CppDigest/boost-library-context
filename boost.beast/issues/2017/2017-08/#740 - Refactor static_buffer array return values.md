# #740 - Refactor static_buffer array return values [Closed]

> Username: vinniefalco  
> Created at: 2017-08-14 12:58:46 UTC  
> Updated at: 2017-09-01 03:32:36 UTC  
> Closed at: 2017-09-01 03:32:36 UTC  
> Url: https://github.com/boostorg/beast/issues/740  

Use a lambda or utility function to return the arrays since the braced-initialization doesn't work right on all compilers. See #739

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-01 03:32:36 UTC  
> Url: https://github.com/boostorg/beast/issues/740#issuecomment-326478605  

Done
