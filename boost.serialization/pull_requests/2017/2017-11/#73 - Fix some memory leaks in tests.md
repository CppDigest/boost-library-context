# #73 Fix some memory leaks in tests [Merged]

> Username: masterleinad  
> Created at: 2017-11-03 00:00:38 UTC  
> Updated at: 2017-11-07 23:04:48 UTC  
> Merged at: 2017-11-07 23:04:48 UTC  
> Closed at: 2017-11-07 23:04:48 UTC  
> Url: https://github.com/boostorg/serialization/pull/73  

All these tests simply miss to call `delete` for memory allocated with `new`.

---
