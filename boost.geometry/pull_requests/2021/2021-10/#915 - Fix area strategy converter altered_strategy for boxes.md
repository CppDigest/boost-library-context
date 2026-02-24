# #915 [strategies] Fix area strategy converter altered_strategy for boxes. [Merged]

> Username: awulkiew  
> Created at: 2021-10-05 21:37:51 UTC  
> Updated at: 2021-10-12 19:05:38 UTC  
> Merged at: 2021-10-12 19:05:38 UTC  
> Closed at: 2021-10-12 19:05:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/915  

This change allows the new box area strategy to be correctly returned from the umbrella strategy returned by the legacy strategy converter. Without this change the general/polygonal area strategy is returned for boxes.

---
