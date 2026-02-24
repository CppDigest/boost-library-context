# #393 - Drop the async_ prefix from connection functions [Open]

> Username: mzimbres  
> Created at: 2026-02-04 21:49:09 UTC  
> Updated at: 2026-02-22 09:31:48 UTC  
> Url: https://github.com/boostorg/redis/issues/393  

I don't see a reason to keep them anymore. The connection will never provide sync functions to there won't be any confusion.   
  
```cpp  
co_await conn.run(cfg);  
co_await conn.exec(req, resp);  
co_await conn.receive();  
```  
  
Look cleaner too.

---

## Comment 1

> Username: anarthal  
> Created at: 2026-02-05 13:42:18 UTC  
> Url: https://github.com/boostorg/redis/issues/393#issuecomment-3853754198  

We'd need to deprecate the `async_` ones. Do you think this adds enough value? Also, `receive` is currently in use by a deprecated function.

---

## Comment 2

> Username: mzimbres  
> Created at: 2026-02-22 09:31:48 UTC  
> Url: https://github.com/boostorg/redis/issues/393#issuecomment-3940580262  

> Do you think this adds enough value?  
  
It does not bring any new functionality to users of course, but I value a lot code that is not verbose, where naming is short and to the point (take linux syscalls naming as an example). I also don't wan't users wondering where is the sync counterpart of each async function. Also, if we port it to Corosio, the same naming should be used.  
  
> Also, receive is currently in use by a deprecated function.  
  
`Receive` was never a good name anyway, we could take the opportunity and name it `wait_push`.
