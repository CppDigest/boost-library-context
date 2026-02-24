# #266 Reduce compilation time by using SkipParser when determining attribute [Merged]

> Username: andreasbuhr  
> Created at: 2025-08-07 11:08:37 UTC  
> Updated at: 2025-10-13 02:31:33 UTC  
> Merged at: 2025-10-13 02:31:32 UTC  
> Closed at: 2025-10-13 02:31:33 UTC  
> Url: https://github.com/boostorg/parser/pull/266  

Reduce compilation time by using SkipParser when determining attribute type.  
  
When using a large parser, the whole tree of parsers in instantiated with the skip (whitespace) parser used. Then, it was instantiated again without a skip parser, just to determine the attribute type.  
This patch changes the code so the attribute type is determined *with* the skip parser. That way, the number of template instantiations required are halved for some use cases.  
  
With gcc 14.2, the instantiations without the skip parser even ended up in the binary. In that case, this patch reduces binary size. This is most likely a compiler bug, as the usage is in decltype().

---

## Comment 1

> Username: tzlaine  
> Created_at: 2025-10-13 02:31:29 UTC  
> Url: https://github.com/boostorg/parser/pull/266#issuecomment-3395670660  

Very nice!  Thanks!

---
