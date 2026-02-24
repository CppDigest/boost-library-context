# #90 fix a bug in try_lock_wrapper<>::operator=() [Merged]

> Username: rick68  
> Created at: 2016-07-13 07:34:08 UTC  
> Updated at: 2016-08-07 11:05:40 UTC  
> Merged at: 2016-08-07 11:05:40 UTC  
> Closed at: 2016-08-07 11:05:40 UTC  
> Url: https://github.com/boostorg/thread/pull/90  

It invokes the copy constructor that already be deleted.

---
