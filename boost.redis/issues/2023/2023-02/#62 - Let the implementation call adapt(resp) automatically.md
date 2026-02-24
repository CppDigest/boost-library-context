# #62 - Let the implementation call adapt(resp) automatically [Closed]

> Username: mzimbres  
> Created at: 2023-02-04 09:53:58 UTC  
> Updated at: 2023-02-05 09:08:06 UTC  
> Closed at: 2023-02-05 09:08:06 UTC  
> Url: https://github.com/boostorg/redis/issues/62  

The fact that users should call `adapt(resp)` was a major source of frustration in the boost review, for example, they have expected  
  
`co_await conn->async_exec(req, resp);`  
  
instead of  
  
`co_await conn->async_exec(req, adapt(resp));`  
  
I admit this makes the library look a bit unpolished. However we can change this easily by offering adapt as a customization point (and renaming it to `boost_redis_adapt`).
