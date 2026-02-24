# #58 - local_weak_ptr ? [Open]

> Username: tower120  
> Created at: 2018-12-14 11:03:49 UTC  
> Updated at: 2018-12-14 16:29:06 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/58  

I do understand that `local_shared_ptr` <=> `shared_ptr` . And that `local_shared_ptr` is basically wrap `shared_ptr`.  
  
But which "weak counter" use `weak_ptr`? Here:  
```  
   local_shared_ptr<X> px(new X());  
   weak_ptr<X> wp(px);  
```  
  
local or atomic? From interface described in documentation - I suppose atomic. Because it's impossible to get `local_shared_ptr` directly from `weak_ptr`.  
  
So... Is it possible to have `local_weak_ptr`?

---

## Comment 1

> Username: pdimov  
> Created at: 2018-12-14 15:25:15 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/58#issuecomment-447357825  

No, it's not possible to have a `local_weak_ptr`. A `local_shared_ptr` may share ownership with a normal `shared_ptr`, or with another `local_shared_ptr` in a different thread. To properly keep track of ownership, the weak pointer has to be atomic.

---

## Comment 2

> Username: tower120  
> Created at: 2018-12-14 16:29:06 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/58#issuecomment-447377508  

I see. Maybe there is need in non-convertible version of `local_shared_ptr`? Plus, I suppose non-convertible version would be more efficient... Something like `unsync_shared_ptr` / `unsync_weak_ptr` ?
