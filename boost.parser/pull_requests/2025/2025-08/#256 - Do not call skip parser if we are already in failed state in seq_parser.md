# #256 Do not call skip parser if we are already in failed state in seq_parser [Merged]

> Username: andreasbuhr  
> Created at: 2025-08-02 13:43:00 UTC  
> Updated at: 2025-10-31 20:41:26 UTC  
> Merged at: 2025-10-31 20:41:26 UTC  
> Closed at: 2025-10-31 20:41:26 UTC  
> Url: https://github.com/boostorg/parser/pull/256  

Now this gave me more than 10% speedup.  
  
Currently, the seq_parser calls the skip-parser for each child, even if we are already failed.

---
