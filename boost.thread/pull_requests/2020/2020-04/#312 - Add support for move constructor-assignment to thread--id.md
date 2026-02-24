# #312 Add support for move constructor/assignment to thread::id [Closed]

> Username: Lastique  
> Created at: 2020-04-04 19:48:05 UTC  
> Updated at: 2020-04-05 14:03:01 UTC  
> Closed at: 2020-04-05 14:03:00 UTC  
> Url: https://github.com/boostorg/thread/pull/312  

Move is more efficient than copy in case if `thread::id` uses a smart pointer internally.  
  
This PR was extracted from https://github.com/boostorg/thread/pull/310.

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-04-05 14:03:00 UTC  
> Url: https://github.com/boostorg/thread/pull/312#issuecomment-609421535  

This should no longer be needed since https://github.com/boostorg/thread/pull/310 was merged.

---
