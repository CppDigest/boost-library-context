# #314 Add define BOOST_PARSER_DISABLE_TRACE to disable trace mode at compil… [Merged]

> Username: tzlaine  
> Created at: 2026-02-14 19:47:30 UTC  
> Updated at: 2026-02-15 21:37:26 UTC  
> Merged at: 2026-02-15 21:37:26 UTC  
> Closed at: 2026-02-15 21:37:26 UTC  
> Url: https://github.com/boostorg/parser/pull/314  

…e time.  
  
The trace feature doubles the compile time, even if never used. This patch introduces the preprocessor define BOOST_PARSER_DISABLE_TRACE to deactivate this feature at compile time.

---

## Comment 1

> Username: andreasbuhr  
> Created_at: 2026-02-14 20:28:01 UTC  
> Url: https://github.com/boostorg/parser/pull/314#issuecomment-3902467032  

cool!

---
