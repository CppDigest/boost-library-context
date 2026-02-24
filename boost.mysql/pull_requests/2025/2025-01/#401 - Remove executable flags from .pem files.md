# #401 Remove executable flags from .pem files [Merged]

> Username: striezel  
> Created at: 2025-01-23 00:53:57 UTC  
> Updated at: 2025-01-25 02:02:18 UTC  
> Merged at: 2025-01-24 14:47:48 UTC  
> Closed at: 2025-01-24 14:47:48 UTC  
> Url: https://github.com/boostorg/mysql/pull/401  

The .pem files should not have the executable flag set.  
  
See <https://github.com/boostorg/admin/issues/47#issuecomment-2575165830> for more information.

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-01-24 10:40:44 UTC  
> Url: https://github.com/boostorg/mysql/pull/401#issuecomment-2612211794  

Thanks for this. There seems to be a failure regarding an upstream change in Boost that uncovered a test error. I'll merge once the build is clean.

---
