# #55 Update to modern style cast [Open]

> Username: bmagistro  
> Created at: 2025-07-29 02:41:02 UTC  
> Updated at: 2025-07-29 02:41:02 UTC  
> Url: https://github.com/boostorg/function/pull/55  

When compiling with clang 19.1.7 on Alma with -Werror and a number of other flags (we run with most warnings enabled), the line is flagged with `error: use of old-style cast [-Werror, -Wold-style-cast]`.  It is unclear if this was just missed or cast style was not updated.   
  
Env:  
Alma 9.6  
Boost 1.88.0 via cmake  
clang 19.1.7

---
