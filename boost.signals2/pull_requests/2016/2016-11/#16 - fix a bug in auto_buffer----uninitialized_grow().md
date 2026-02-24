# #16 fix a bug in auto_buffer<>::uninitialized_grow() [Merged]

> Username: rick68  
> Created at: 2016-11-09 10:46:46 UTC  
> Updated at: 2016-11-09 22:28:34 UTC  
> Merged at: 2016-11-09 22:28:34 UTC  
> Closed at: 2016-11-09 22:28:34 UTC  
> Url: https://github.com/boostorg/signals2/pull/16  

It's not growing capacity when we want more.

---
