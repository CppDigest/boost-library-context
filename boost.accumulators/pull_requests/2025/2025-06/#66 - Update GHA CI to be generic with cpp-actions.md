# #66 Update GHA CI to be generic with cpp-actions. [Closed]

> Username: grafikrobot  
> Created at: 2025-06-05 18:05:07 UTC  
> Updated at: 2025-06-06 23:13:05 UTC  
> Closed at: 2025-06-06 23:13:05 UTC  
> Url: https://github.com/boostorg/accumulators/pull/66  

This rewrites the GHA CI to use https://github.com/alandefreitas/cpp-actions. It allows the CI setup to remain generic enough to flexibly handle changes to upstream GHA changes. Of course, assuming that cpp-actions adjusts accordingly. With that it also expands the testing coverage.

---
