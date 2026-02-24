# #54 - Core metafunctions should return IntegralConstants [Closed]

> Username: ldionne  
> Created at: 2015-04-22 01:58:54 UTC  
> Updated at: 2016-11-17 08:13:39 UTC  
> Closed at: 2016-11-17 08:13:39 UTC  
> Url: https://github.com/boostorg/hana/issues/54  

All the boolean metafunctions in Core should return IntegralConstants. Unfortunately, this raises issues of circular dependencies. Or perhaps core should be minimal and not use IntegralConstants at all?  
  
Initially opened as #51.

---

## Comment 1

> Username: ldionne  
> Created at: 2016-11-17 08:13:39 UTC  
> Url: https://github.com/boostorg/hana/issues/54#issuecomment-261182341  

Closing this issue in favor of pr #310.
