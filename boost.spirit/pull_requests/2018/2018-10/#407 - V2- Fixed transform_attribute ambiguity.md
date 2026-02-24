# #407 V2: Fixed transform_attribute ambiguity [Merged]

> Username: Kojoley  
> Created at: 2018-10-14 20:03:39 UTC  
> Updated at: 2018-10-23 02:11:37 UTC  
> Merged at: 2018-10-23 02:10:48 UTC  
> Closed at: 2018-10-23 02:10:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/407  

Domain-agnostic class template partial specializations and  
type agnostic domain partial specializations are ambiguous.  
To resolve the ambiguity type agnostic domain partial  
specializations are dispatched via intermediate type.  
  
Fixes #396.

---
