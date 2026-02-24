# #152 - Handle EINTR in shared_memory_object [Closed]

> Username: Lastique  
> Created at: 2021-09-15 12:11:49 UTC  
> Updated at: 2024-10-04 10:53:17 UTC  
> Closed at: 2024-10-04 10:52:59 UTC  
> Url: https://github.com/boostorg/interprocess/issues/152  

As documented, [`shm_open`](https://pubs.opengroup.org/onlinepubs/007904875/functions/shm_open.html) may return `EINTR` if interrupted by a signal. This error code is not specially handled in `shared_memory_object::priv_open_or_create` and will result in an exception being thrown. It would be better if the `shm_open` call was attempted again internally by `shared_memory_object` without an exception.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-10-04 10:53:16 UTC  
> Url: https://github.com/boostorg/interprocess/issues/152#issuecomment-2393424323  

Many thanks for the report.
