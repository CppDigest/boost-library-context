# #34 Catch boost::lock_error in destructor as destructors may not throw [Closed]

> Username: tempoz  
> Created at: 2014-12-08 18:42:07 UTC  
> Updated at: 2015-02-17 22:40:31 UTC  
> Closed at: 2015-02-17 22:40:31 UTC  
> Url: https://github.com/boostorg/thread/pull/34  

This is intended to prevent the possibility of ~unique_lock throwing a lock_error, which would terminate any program in which it occurred.

---
