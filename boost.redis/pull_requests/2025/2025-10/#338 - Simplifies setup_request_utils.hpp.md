# #338 Simplifies setup_request_utils.hpp [Merged]

> Username: anarthal  
> Created at: 2025-10-23 16:48:59 UTC  
> Updated at: 2025-10-23 20:37:54 UTC  
> Merged at: 2025-10-23 20:37:50 UTC  
> Closed at: 2025-10-23 20:37:50 UTC  
> Url: https://github.com/boostorg/redis/pull/338  

Removes unused clear_response  
Removes include in connection.hpp  
Moves the header to impl/ and makes functions inline

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-10-23 16:50:00 UTC  
> Url: https://github.com/boostorg/redis/pull/338#issuecomment-3438069433  

This is a small simplification with no functional changes. Since we only use the setup request utils in .ipp files, we can completely remove the header from detail/.

---
