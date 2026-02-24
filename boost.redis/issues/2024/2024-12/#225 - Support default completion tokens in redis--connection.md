# #225 - Support default completion tokens in redis::connection [Closed]

> Username: anarthal  
> Created at: 2024-12-19 11:55:10 UTC  
> Updated at: 2025-02-08 21:26:12 UTC  
> Closed at: 2025-02-08 21:26:12 UTC  
> Url: https://github.com/boostorg/redis/issues/225  

The non-templated connection should have `asio::deferred` as the default completion token for all its operations, so the following works:  
  
```cpp  
redis::connection conn (ctx);  
// ...  
co_await conn.async_exec(req, res);  
```

---

## Comment 1

> Username: mzimbres  
> Created at: 2024-12-22 20:39:54 UTC  
> Url: https://github.com/boostorg/redis/issues/225#issuecomment-2558591854  

Hi, I did have default completion tokens but somehow they went missing after a refactoring. Will do it when I have the time. Thanks.
