# #41 test_try_push() fix and channel::is_closed() wording [Merged]

> Username: nat-goodspeed  
> Created at: 2015-08-05 11:55:35 UTC  
> Updated at: 2016-06-06 11:36:33 UTC  
> Merged at: 2015-08-05 12:07:25 UTC  
> Closed at: 2015-08-05 12:07:25 UTC  
> Url: https://github.com/boostorg/fiber/pull/41  

I realize channel::is_closed() is likely to vanish soon, but until it does, we should describe its effects properly to avoid confusion.  
  
test_try_push() should call try_push() rather than push().

---
