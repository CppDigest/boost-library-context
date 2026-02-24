# #146 - Pass socket close errors to the caller [Closed]

> Username: mzimbres  
> Created at: 2023-08-26 13:44:55 UTC  
> Updated at: 2023-08-31 10:53:13 UTC  
> Closed at: 2023-08-31 10:53:13 UTC  
> Url: https://github.com/boostorg/redis/issues/146  

We are currently calling the throwing overload of the `socket.close()` function, which might lead to crashes in recoverable situations. We should use the overload that takes error_code and pass it to the caller of `cancel()`.  
  
See https://github.com/boostorg/redis/blob/509635f2227ef6f3c299d9d765fe1c8f385391bb/include/boost/redis/detail/connection_base.hpp#L722

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-08-31 10:53:10 UTC  
> Url: https://github.com/boostorg/redis/issues/146#issuecomment-1700814628  

Thinking more, the errors that occur when closing a tcp stream have only logging purposes and I don't see much need in returning them to the caller since they won't be used for any error handing, even logging it seems unnecessary. I will close this ticket and wait more before implementing this feature.
