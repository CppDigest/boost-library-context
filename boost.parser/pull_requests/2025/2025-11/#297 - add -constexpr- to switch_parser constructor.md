# #297 add "constexpr" to switch_parser constructor [Merged]

> Username: andreasbuhr  
> Created at: 2025-11-24 13:29:22 UTC  
> Updated at: 2025-11-25 18:02:18 UTC  
> Merged at: 2025-11-25 18:02:18 UTC  
> Closed at: 2025-11-25 18:02:18 UTC  
> Url: https://github.com/boostorg/parser/pull/297  

so switch(predicate)(valA, parserA)(valB, parserB) can result in a constexpr object.

---

## Comment 1

> Username: tzlaine  
> Created_at: 2025-11-25 06:41:37 UTC  
> Url: https://github.com/boostorg/parser/pull/297#issuecomment-3573989549  

This looks pretty straightforward.  I would want at least one test that shows this working before merging though.

---

## Comment 2

> Username: andreasbuhr  
> Created_at: 2025-11-25 08:42:54 UTC  
> Url: https://github.com/boostorg/parser/pull/297#issuecomment-3574409346  

good point. Test added.

---

## Comment 3

> Username: tzlaine  
> Created_at: 2025-11-25 18:02:14 UTC  
> Url: https://github.com/boostorg/parser/pull/297#issuecomment-3576867886  

Looks good; thanks!

---
