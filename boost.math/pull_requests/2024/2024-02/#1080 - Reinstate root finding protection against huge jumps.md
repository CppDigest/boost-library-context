# #1080 Reinstate root finding protection against huge jumps. [Merged]

> Username: jzmaddock  
> Created at: 2024-02-05 19:17:01 UTC  
> Updated at: 2024-02-06 12:34:40 UTC  
> Merged at: 2024-02-06 12:34:39 UTC  
> Closed at: 2024-02-06 12:34:39 UTC  
> Url: https://github.com/boostorg/math/pull/1080  

Apply error handling more rigorously to any root finding client. Mark evaluation_error's as not reachable for code coverage. Fixes https://github.com/boostorg/math/issues/184.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2024-02-06 12:34:33 UTC  
> Url: https://github.com/boostorg/math/pull/1080#issuecomment-1929434297  

Failures are unrelated... @NAThompson condition_number_test is failing due to over-large object size on mingw, we have to split in two later if this persists.

---
