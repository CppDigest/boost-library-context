# #202 Allow to map message_queue in anonymous memory [Merged]

> Username: intuibase  
> Created at: 2023-09-12 18:00:49 UTC  
> Updated at: 2024-07-06 21:23:27 UTC  
> Merged at: 2024-07-06 21:23:13 UTC  
> Closed at: 2024-07-06 21:23:13 UTC  
> Url: https://github.com/boostorg/interprocess/pull/202  

The goal of this feature is to allow to map message_queue in anonymous shared memory - to make communication of forked processes easy. There is no need to create a "file" mapping in this scenario - there is no issues with privileges and memory is not exposed.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2024-07-06 21:23:26 UTC  
> Url: https://github.com/boostorg/interprocess/pull/202#issuecomment-2211973115  

Many thanks for the feature.

---
