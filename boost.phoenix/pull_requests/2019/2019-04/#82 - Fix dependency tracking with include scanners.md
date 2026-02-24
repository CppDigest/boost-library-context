# #82 Fix dependency tracking with include scanners. [Closed]

> Username: EricWF  
> Created at: 2019-04-11 18:52:41 UTC  
> Updated at: 2024-03-10 04:29:09 UTC  
> Closed at: 2024-03-10 04:29:09 UTC  
> Url: https://github.com/boostorg/phoenix/pull/82  

Some include scanners can't see through #include MACRO_EXPANSION,  
so they fail to establish the dependency on the actual header.  
  
This patch addresses the issue by showing the includes to the scanner  
in a always-disabled #if block.  
  
There are several other libraries in boost that have this fix, see:  
https://github.com/boostorg/config/commit/3dd3a9c46c53282ddc67a0c76f1c1bed7c4d3028  
https://github.com/boostorg/utility/commit/21261a8630e0d4ae841831330f49bd13e002a773  
https://github.com/boostorg/math/commit/3f6394139c464919519f1729259355818ee8c6ce

---

## Comment 1

> Username: Flast  
> Created_at: 2019-04-17 23:07:59 UTC  
> Url: https://github.com/boostorg/phoenix/pull/82#issuecomment-484294428  

Good point, but config.hpp affects entire of phoenix. It should be placed in each header which is actual depends on.

---
