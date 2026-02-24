# #322 Makes the CI run part of the examples [Merged]

> Username: anarthal  
> Created at: 2025-10-03 11:26:39 UTC  
> Updated at: 2025-10-03 18:09:01 UTC  
> Merged at: 2025-10-03 18:08:58 UTC  
> Closed at: 2025-10-03 18:08:58 UTC  
> Url: https://github.com/boostorg/redis/pull/322  

Removes a conditional from CMake that was causing examples to never be run under CI.

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-10-03 16:53:23 UTC  
> Url: https://github.com/boostorg/redis/pull/322#issuecomment-3366445118  

This is straightforward: we weren't running any examples in the CI because of an incorrect CMake setting, and this fixes it. The TLS example references your occase.de server, which seems to be down, so I've set it to not testable.

---
