# #330 Move t instead of copying the value [Open]

> Username: venkat-murty  
> Created at: 2025-06-08 16:08:56 UTC  
> Updated at: 2025-06-08 16:08:56 UTC  
> Url: https://github.com/boostorg/serialization/pull/330  

`std::optional<T>` does not compile when T has a move operator and the copy operator is deleted.

---
