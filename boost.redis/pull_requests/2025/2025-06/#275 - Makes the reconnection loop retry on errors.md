# #275 Makes the reconnection loop retry on errors [Merged]

> Username: anarthal  
> Created at: 2025-06-22 20:01:40 UTC  
> Updated at: 2025-06-29 17:02:25 UTC  
> Merged at: 2025-06-29 17:02:22 UTC  
> Closed at: 2025-06-29 17:02:22 UTC  
> Url: https://github.com/boostorg/redis/pull/275  

After an error is encountered during name resolution, connection establishment, SSL handshake, Redis hello, reading or writing, async_run will now keep retrying if the configuration requests it  
  
close #255

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-06-22 20:02:16 UTC  
> Url: https://github.com/boostorg/redis/pull/275#issuecomment-2994417135  

This PR requires #273

---

## Comment 2

> Username: anarthal  
> Created_at: 2025-06-26 10:15:15 UTC  
> Url: https://github.com/boostorg/redis/pull/275#issuecomment-3007963148  

I've performed manual testing, both with and without TLS, verifying that retries are performed both on connection errors and on read errors (by killing the server in the middle of an operation).

---
