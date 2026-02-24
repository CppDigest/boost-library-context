# #379 Removed brittle checks in connection_pool_cancel test [Merged]

> Username: anarthal  
> Created at: 2024-11-04 09:44:40 UTC  
> Updated at: 2024-11-04 12:13:38 UTC  
> Merged at: 2024-11-04 12:13:37 UTC  
> Closed at: 2024-11-04 12:13:37 UTC  
> Url: https://github.com/boostorg/mysql/pull/379  

These checks could cause false positive race conditions to be reported.

---
