# #20 Do not return dangling pointer to exception message [Merged]

> Username: nbougalis  
> Created at: 2015-04-10 20:31:20 UTC  
> Updated at: 2015-04-25 14:05:34 UTC  
> Merged at: 2015-04-25 14:05:34 UTC  
> Closed at: 2015-04-25 14:05:34 UTC  
> Url: https://github.com/boostorg/coroutine/pull/20  

The `coroutine_error` class implemented the `what` function in a way that would cause undefined behavior if the returned pointer was ever used, since the object into which it was pointing was a temporary `std::string` that would have already been destroyed before the `what` function returned.

---

## Comment 1

> Username: olk  
> Created_at: 2015-04-25 14:05:30 UTC  
> Url: https://github.com/boostorg/coroutine/pull/20#issuecomment-96210088  

thx

---
