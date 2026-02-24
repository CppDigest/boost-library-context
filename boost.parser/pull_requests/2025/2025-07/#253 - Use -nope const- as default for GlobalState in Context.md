# #253 Use "nope const" as default for GlobalState in Context [Closed]

> Username: andreasbuhr  
> Created at: 2025-07-28 12:18:28 UTC  
> Updated at: 2025-10-13 02:05:12 UTC  
> Closed at: 2025-10-13 02:05:12 UTC  
> Url: https://github.com/boostorg/parser/pull/253  

Do not merge as-is.  
  
This MR is just to illustrate the problem described in https://github.com/boostorg/parser/issues/250.  
  
These are the modifications I did to alleviate the problem on my side. With these modifications, the GlobalState template parameter of the Context is consistently "nope const". This reduces the number of template instantiations.

---
