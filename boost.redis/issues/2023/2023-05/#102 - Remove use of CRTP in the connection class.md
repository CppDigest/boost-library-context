# #102 - Remove use of CRTP in the connection class [Closed]

> Username: mzimbres  
> Created at: 2023-05-16 17:51:13 UTC  
> Updated at: 2023-05-20 16:27:00 UTC  
> Closed at: 2023-05-20 16:27:00 UTC  
> Url: https://github.com/boostorg/redis/issues/102  

After the unification of `ssl::connection` and the plain `connection` there is no need anymore for using CRTP. It should be removed as it only makes the code less clear.

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-05-20 16:27:00 UTC  
> Url: https://github.com/boostorg/redis/issues/102#issuecomment-1555946487  

Implemented in https://github.com/boostorg/redis/commit/7abfc5fd8df06d9f7d53b569fa57f16f4b599a0f.
