# #366 - http::header::fields metadata overhaul [Closed]

> Username: vinniefalco  
> Created at: 2017-05-18 15:35:23 UTC  
> Updated at: 2017-06-21 01:59:15 UTC  
> Closed at: 2017-06-21 01:59:15 UTC  
> Url: https://github.com/boostorg/beast/issues/366  

The implementation should use ADL compatible compile-time virtual functions for reading and writing all known fields:  
* Connection:  
* Content-Length  
* Transfer-Encoding  
* Upgrade

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-21 01:59:15 UTC  
> Url: https://github.com/boostorg/beast/issues/366#issuecomment-309940898  

This is done
