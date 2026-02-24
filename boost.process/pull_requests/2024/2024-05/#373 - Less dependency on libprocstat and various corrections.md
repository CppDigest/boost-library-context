# #373 Less dependency on libprocstat and various corrections. [Merged]

> Username: ghost  
> Created at: 2024-05-22 21:33:32 UTC  
> Updated at: 2024-06-04 01:22:52 UTC  
> Merged at: 2024-06-04 00:03:00 UTC  
> Closed at: 2024-06-04 00:03:00 UTC  
> Url: https://github.com/boostorg/process/pull/373  

On FreeBSD I would like to remove dependency on libprocstat completely, and replace it with libkvm to be more like the other *BSD platforms in terms of linker dependencies. This pull request is our first move in that direction.

---
