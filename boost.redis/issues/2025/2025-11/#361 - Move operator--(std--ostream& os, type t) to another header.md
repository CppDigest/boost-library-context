# #361 - Move operator<<(std::ostream& os, type t) to another header [Closed]

> Username: mzimbres  
> Created at: 2025-11-30 13:35:00 UTC  
> Updated at: 2025-12-02 10:20:33 UTC  
> Closed at: 2025-12-02 10:20:33 UTC  
> Url: https://github.com/boostorg/redis/issues/361  

We should move https://github.com/boostorg/redis/blob/755d14a10dd8460a7a6ce947cf33d83428fa797c/include/boost/redis/resp3/type.hpp#L75 the  to another header to avoid including iostream in user code. At the moment it is unavoidable for users.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-11-30 16:08:06 UTC  
> Updated at: 2025-11-30 16:08:32 UTC  
> Url: https://github.com/boostorg/redis/issues/361#issuecomment-3592755467  

How about replacing `<ostream>` by `<iosfwd>`, which is much, much more lightweight? This way we avoid breaking existing code.
