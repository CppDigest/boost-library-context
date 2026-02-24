# #28 Use throw() when noexcept(true) is not available. [Closed]

> Username: uecasm  
> Created at: 2016-11-08 06:08:30 UTC  
> Updated at: 2016-11-08 23:00:46 UTC  
> Closed at: 2016-11-08 23:00:46 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/28  

Only applied to constructors, destructors, and assignment operators, which may  
still be detectable via compiler magic when noexcept(expr) is not implemented.  
  
Replacement for PR #27.  (It wouldn't let me reopen that for some reason.)

---

## Comment 1

> Username: pdimov  
> Created_at: 2016-11-08 16:50:15 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/28#issuecomment-259191684  

I implemented something loosely along those lines in  
  
https://github.com/boostorg/smart_ptr/commit/3e61a63f60fe14f867eb4579c369e53d875ec0f9

---

## Comment 2

> Username: uecasm  
> Created_at: 2016-11-08 23:00:46 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/28#issuecomment-259286876  

Great, looks good, thanks.

---
