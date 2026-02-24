# #54 Add circumvent for zero-variadic macros  - solve #43 [Open]

> Username: Horolsky  
> Created at: 2025-07-07 15:18:38 UTC  
> Updated at: 2025-07-08 08:25:40 UTC  
> Url: https://github.com/boostorg/describe/pull/54  

It would be nice to resolve this issue, since there are many areas where standards require overly strict rules like `-Wgnu-zero-variadic-macro-arguments`, and the preprocessor is not easily isolated for suppression.  
  
This fix is based on:  
 - Using `void` for types with empty bases, highly frequent usecase  
 - `_EMPTY` member macros, ugly (exposing few internals as library API), but functional.

---

## Comment 1

> Username: Horolsky  
> Created_at: 2025-07-08 08:25:40 UTC  
> Url: https://github.com/boostorg/describe/pull/54#issuecomment-3047875380  

Hi @pdimov , I have doubts about describing circumvents in the main doc, so if you find the solution itself good enough, I guess it could be delivered as a hidden feature with instructions only in gh issue

---
