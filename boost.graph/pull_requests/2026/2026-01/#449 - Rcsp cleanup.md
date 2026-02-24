# #449 Rcsp cleanup [Open]

> Username: andreacassioli  
> Created at: 2026-01-15 16:20:38 UTC  
> Updated at: 2026-01-19 23:07:01 UTC  
> Url: https://github.com/boostorg/graph/pull/449  

In this PR I introduce a breaking change to the RCSP algorithm interface along several modernization of the code  
  
Breaking change:  
* the RCSP interface does NOT require an edge map (it never had) and thus it should not be required. Thus I remove it all along.  
  
Other changes:  
* the edge feasibility flag is moved to be local in to the extension function evaluation  
* when accessing the final node list at destination, use a reference instead of a copy  
* when a single solution is asked for, move it instead of copy.  
* removed macro selecting the allocator deponding on C++ version is obsolete because it tests C++11 which is not supported.  
* docs updated  
  
Modernization  
   
* the code is simplified by using `auto` extensively, that allows fewer type definitions and less verbose code  
* some `typedef` has been rewritten with `using`  
* use `std::tie` instead of `boost::tie`

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2026-01-19 23:07:01 UTC  
> Url: https://github.com/boostorg/graph/pull/449#issuecomment-3770383588  

Great, thanks, I'll try to have a close look soon.

---
