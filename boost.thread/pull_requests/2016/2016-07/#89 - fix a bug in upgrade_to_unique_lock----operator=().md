# #89 fix a bug in upgrade_to_unique_lock<>::operator=() [Merged]

> Username: rick68  
> Created at: 2016-07-13 04:42:27 UTC  
> Updated at: 2016-07-13 05:00:51 UTC  
> Merged at: 2016-07-13 05:00:39 UTC  
> Closed at: 2016-07-13 05:00:39 UTC  
> Url: https://github.com/boostorg/thread/pull/89  

It invokes the copy constructor that already be deleted.

---

## Comment 1

> Username: viboes  
> Created_at: 2016-07-13 05:00:32 UTC  
> Url: https://github.com/boostorg/thread/pull/89#issuecomment-232256844  

Oups!

---

## Comment 2

> Username: viboes  
> Created_at: 2016-07-13 05:00:51 UTC  
> Url: https://github.com/boostorg/thread/pull/89#issuecomment-232256880  

Thanks again.

---
