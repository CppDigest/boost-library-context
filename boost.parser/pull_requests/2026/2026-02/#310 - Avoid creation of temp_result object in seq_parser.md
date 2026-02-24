# #310 Avoid creation of temp_result object in seq_parser [Merged]

> Username: andreasbuhr  
> Created at: 2026-02-03 09:40:55 UTC  
> Updated at: 2026-02-14 19:35:50 UTC  
> Merged at: 2026-02-14 19:35:49 UTC  
> Closed at: 2026-02-14 19:35:49 UTC  
> Url: https://github.com/boostorg/parser/pull/310  

The object is never used, only its type is used.

---

## Comment 1

> Username: tzlaine  
> Created_at: 2026-02-14 19:35:46 UTC  
> Url: https://github.com/boostorg/parser/pull/310#issuecomment-3902397293  

This is a nice improvement, thanks.

---
