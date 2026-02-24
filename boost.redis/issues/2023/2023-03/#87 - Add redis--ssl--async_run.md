# #87 - Add redis::ssl::async_run [Closed]

> Username: mzimbres  
> Created at: 2023-03-16 20:17:23 UTC  
> Updated at: 2023-05-06 19:01:49 UTC  
> Closed at: 2023-05-06 19:01:48 UTC  
> Url: https://github.com/boostorg/redis/issues/87  

There should be the equivalent of redis::async_run for tls streams redis::ssl::async_run. The only difference is that the later is supposed to do ssl handshake in addition to the other operations.

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-05-06 19:01:48 UTC  
> Url: https://github.com/boostorg/redis/issues/87#issuecomment-1537203262  

This issue became obsolete after https://github.com/boostorg/redis/commit/2d53bb748e5dd6dab4c4ff7d03e65c2276b48cbc.
