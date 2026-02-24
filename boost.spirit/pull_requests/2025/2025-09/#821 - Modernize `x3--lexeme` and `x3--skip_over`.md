# #821 Modernize `x3::lexeme` and `x3::skip_over` [Merged]

> Username: saki7  
> Created at: 2025-09-10 00:06:11 UTC  
> Updated at: 2025-09-10 00:14:29 UTC  
> Merged at: 2025-09-10 00:13:56 UTC  
> Closed at: 2025-09-10 00:13:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/821  

Part of boostorg/spirit_x4#4   
  
Use concepts in `x3::lexeme` and `x3::skip_over`. `x3::lexeme` is now a CPO that inhibits ADL.

---

## Comment 1

> Username: saki7  
> Created_at: 2025-09-10 00:14:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/821#issuecomment-3272739876  

Self-merged; these are trivial changes and does not change the semantics of the public API.

---
