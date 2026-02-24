# #255 Performance improvement: Do not copy use_parser into lambda [Merged]

> Username: andreasbuhr  
> Created at: 2025-08-02 13:17:07 UTC  
> Updated at: 2025-10-13 02:16:49 UTC  
> Merged at: 2025-10-13 02:16:48 UTC  
> Closed at: 2025-10-13 02:16:48 UTC  
> Url: https://github.com/boostorg/parser/pull/255  

I don't see a reason why the use_parser structure is copied into the lambda. It is not used outside of the lambda, so the lambda might as well use the object existing outside.  
  
This gave me a few percent performance improvement. The use_parser_t structure is quite heavy.

---
