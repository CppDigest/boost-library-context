# #278 - Attribute of a parser made with an if_ directive doesn't match documentation [Closed]

> Username: biljazovic  
> Created at: 2025-09-15 10:05:50 UTC  
> Updated at: 2025-10-13 01:43:39 UTC  
> Closed at: 2025-10-13 01:43:39 UTC  
> Url: https://github.com/boostorg/parser/issues/278  

Per the documentation, the attribute of `if_(pred)[p]` is `std::optional<ATTR(p)>`.  
  
However, in practice it is actually `ATTR(p)`, which matches its implementation as `eps(pred) >> p`.  
  
That said, it seems more natural for it to be equivalent to `eps(std::not_fn(pred)) | p`, i.e. if the predicate is false, the parser succeeds without consuming input. Then the attribute type would indeed be `std::optional<ATTR(p)>`.

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-10-13 01:42:57 UTC  
> Url: https://github.com/boostorg/parser/issues/278#issuecomment-3395615423  

Thanks very much for pointing this out.  The docs are just plain wrong.
