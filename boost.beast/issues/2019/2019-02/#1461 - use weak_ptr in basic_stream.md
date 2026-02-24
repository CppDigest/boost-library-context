# #1461 - use weak_ptr in basic_stream [Closed]

> Username: vinniefalco  
> Created at: 2019-02-19 17:29:20 UTC  
> Updated at: 2022-06-16 16:21:34 UTC  
> Closed at: 2022-06-16 16:21:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1461  

self-explanatory

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-20 15:55:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1461#issuecomment-465636911  

Tried it and rolled it back. The problem is that the `pending_guard` wants to clear the bool in the async op destructor, but if the impl has already been destroyed then of course this is undefined behavior. We could try to acquire the lock but that's an expensive extra atomic operation on every destruction which is not friendly.  
  
It is possible that refactoring the ops to not wait on the timer every time (i.e. the timer is never canceled) could make this problem more tractable.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-06-16 16:21:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1461#issuecomment-1157869685  

We aren't going to do this anymore.
