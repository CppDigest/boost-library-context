# #404 Change assert() to static_assert() in copy_async() [Merged]

> Username: kylelutz  
> Created at: 2014-12-31 05:04:36 UTC  
> Updated at: 2014-12-31 06:37:36 UTC  
> Merged at: 2014-12-31 06:37:35 UTC  
> Closed at: 2014-12-31 06:37:35 UTC  
> Url: https://github.com/boostorg/compute/pull/404  

This changes the assert() for contiguous host iterators in  
the copy_async() algorithm to a static_assert() so that this  
currently unsupported case can be caught at compile-time.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-12-31 05:21:15 UTC  
> Url: https://github.com/boostorg/compute/pull/404#issuecomment-68424387  

[![Coverage Status](https://coveralls.io/builds/1676558/badge)](https://coveralls.io/builds/1676558)  
  
Coverage remained the same when pulling **5b5eae82704b80543579fc57d02eb0a8f70d8ec3 on copy-async-static-assert** into **fa0f429d104f4a5ba8a290e61775cd4b04dba678 on develop**.

---
