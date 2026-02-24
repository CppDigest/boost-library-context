# #334 Changes request default's cancel_on_connection_lost to false [Merged]

> Username: anarthal  
> Created at: 2025-10-17 14:26:32 UTC  
> Updated at: 2025-10-20 14:37:05 UTC  
> Merged at: 2025-10-20 14:37:02 UTC  
> Closed at: 2025-10-20 14:37:02 UTC  
> Url: https://github.com/boostorg/redis/pull/334  

Changes request default constructor to set cancel_on_connection_lost to false, matching request::config's default initializer  
Overwrites this flag to true for the setup request  
Adds unit tests for the latter

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-10-17 14:29:18 UTC  
> Url: https://github.com/boostorg/redis/pull/334#issuecomment-3415842155  

This is a fix for #329, where I missed that the request constructor's default argument passes the flag values explicitly. I forgot to update this value, effectively rendering the PR ineffective in most cases. Apparently passing these flags explicitly is required because `config` is a nested type, rather than a standalone type.  
  
I've also made sure to overwrite the flags for the setup request, so it's cancelled on error.

---
