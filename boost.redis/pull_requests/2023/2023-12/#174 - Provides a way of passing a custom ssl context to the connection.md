# #174 Provides a way of passing a custom ssl context to the connection. [Merged]

> Username: mzimbres  
> Created at: 2023-12-31 15:18:38 UTC  
> Updated at: 2024-01-03 16:20:08 UTC  
> Merged at: 2024-01-03 16:20:00 UTC  
> Closed at: 2024-01-03 16:20:00 UTC  
> Url: https://github.com/boostorg/redis/pull/174  

@anarthal It would be great if you had a look at this PR. Thanks.

---

## Comment 1

> Username: anarthal  
> Created_at: 2023-12-31 16:10:15 UTC  
> Url: https://github.com/boostorg/redis/pull/174#issuecomment-1872984979  

It looks like moving a `connection` constructed from a null context leaves a dangling pointer to the fallback context. Plus you're creating a context per object even if you don't need one. If you're gonna do like that, better pass the context by value.  
  
I solved both of these issues by having the fallback context as a singleton. Klemens solved it using an Asio service.

---

## Comment 2

> Username: mzimbres  
> Created_at: 2024-01-01 11:16:39 UTC  
> Url: https://github.com/boostorg/redis/pull/174#issuecomment-1873282207  

@mzimbres I am glad I pulled you in the PR. I have been hurrying with some implementations as my free time has dropped.

---

## Comment 3

> Username: anarthal  
> Created_at: 2024-01-01 14:59:22 UTC  
> Url: https://github.com/boostorg/redis/pull/174#issuecomment-1873360782  

I also recommend using `ssl::context::tlsv12_client` as the default method. This disables support for TLS 1.0 and 1.1, which are considered vulnerable.

---
