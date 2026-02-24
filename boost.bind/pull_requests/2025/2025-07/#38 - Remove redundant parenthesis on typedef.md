# #38 Remove redundant parenthesis on typedef [Merged]

> Username: bmagistro  
> Created at: 2025-07-29 01:47:30 UTC  
> Updated at: 2026-01-03 12:35:01 UTC  
> Merged at: 2026-01-03 12:35:01 UTC  
> Closed at: 2026-01-03 12:35:01 UTC  
> Url: https://github.com/boostorg/bind/pull/38  

When compiling with clang 19.1.7 on Alma with `-Werror` and a number of other flags (we run with most warnings enabled), the line is flagged with `error: redundant parentheses surrounding declarator [-Werror, -Wredundant-parens]`.  
  
Env:  
Alma 9.6  
Boost 1.88.0 via cmake  
clang 19.1.7

---

## Comment 1

> Username: pdimov  
> Created_at: 2026-01-03 12:16:27 UTC  
> Url: https://github.com/boostorg/bind/pull/38#issuecomment-3707016541  

I seem to have missed this, sorry.

---
