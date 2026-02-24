# #1408 - Session allocator should be thread-safe [Closed]

> Username: djarek  
> Created at: 2019-01-09 20:35:00 UTC  
> Updated at: 2019-01-15 20:31:27 UTC  
> Closed at: 2019-01-15 20:31:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1408  

https://github.com/boostorg/beast/blob/397c388e18415798847cbcc574ad8ff95633a233/example/common/session_alloc.hpp#L26  
  
The TS requires allocation to be thread-safe, because it can be performed in a thread that's internal to the implementation ([async.reqmts.async.alloc]).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-01-10 00:16:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1408#issuecomment-452922056  

@glenfe confirm or deny?

---

## Comment 2

> Username: glenfe  
> Created at: 2019-01-10 01:15:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1408#issuecomment-452933517  

Confirm
