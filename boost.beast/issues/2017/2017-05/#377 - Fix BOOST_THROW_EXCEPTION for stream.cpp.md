# #377 - Fix BOOST_THROW_EXCEPTION for stream.cpp [Closed]

> Username: vinniefalco  
> Created at: 2017-05-23 14:25:15 UTC  
> Updated at: 2017-09-01 03:13:44 UTC  
> Closed at: 2017-09-01 03:13:44 UTC  
> Url: https://github.com/boostorg/beast/issues/377  

Using `BOOST_THROW_EXCEPTION` instead of straight `throw` causes address sanitizer errors.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-01 03:13:44 UTC  
> Url: https://github.com/boostorg/beast/issues/377#issuecomment-326476675  

Not really a problem anymore
