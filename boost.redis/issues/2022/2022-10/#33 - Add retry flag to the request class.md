# #33 - Add retry flag to the request class [Closed]

> Username: mzimbres  
> Created at: 2022-10-03 18:09:40 UTC  
> Updated at: 2022-10-13 19:57:11 UTC  
> Closed at: 2022-10-13 19:57:11 UTC  
> Url: https://github.com/boostorg/redis/issues/33  

The `retry` flag should express the user desire to retry sending requests that have been sent but remained unresponded after a connection lost. For example, users may want to retry `GET` commands but not `SET` commands.

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-10-13 19:57:11 UTC  
> Url: https://github.com/boostorg/redis/issues/33#issuecomment-1278113422  

Implemented in https://github.com/mzimbres/aedis/commit/770e224917e4f3afea2b244cf1448d19f052873c.
