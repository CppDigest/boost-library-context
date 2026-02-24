# #139 - Connection pool [Closed]

> Username: banderzhm  
> Created at: 2023-08-14 05:10:04 UTC  
> Updated at: 2023-08-25 19:27:35 UTC  
> Closed at: 2023-08-25 19:27:35 UTC  
> Url: https://github.com/boostorg/redis/issues/139  

Will Redis connection pooling be supported in the future?

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-08-14 06:27:06 UTC  
> Url: https://github.com/boostorg/redis/issues/139#issuecomment-1676750011  

Boost.Redis is async and implements command multiplexing, that means it does not need a connection pool. In fact, a connection pool may slow down you application since it prevents bigger command pipelines.

---

## Comment 2

> Username: mzimbres  
> Created at: 2023-08-22 06:27:37 UTC  
> Url: https://github.com/boostorg/redis/issues/139#issuecomment-1687537984  

Do you need any more info? Can we close this ticket?

---

## Comment 3

> Username: banderzhm  
> Created at: 2023-08-25 07:30:49 UTC  
> Url: https://github.com/boostorg/redis/issues/139#issuecomment-1692901826  

Okay, thank you for the answer
