# #16 fix a bug in ios_state_ptr<>::release() [Merged]

> Username: rick68  
> Created at: 2016-06-20 17:45:07 UTC  
> Updated at: 2016-06-28 12:07:54 UTC  
> Merged at: 2016-06-28 12:07:46 UTC  
> Closed at: 2016-06-28 12:07:46 UTC  
> Url: https://github.com/boostorg/chrono/pull/16  

This member function should transfer the ownership to caller, but it returns a dangling pointer.

---

## Comment 1

> Username: viboes  
> Created_at: 2016-06-28 12:07:54 UTC  
> Url: https://github.com/boostorg/chrono/pull/16#issuecomment-229030164  

Thanks

---
