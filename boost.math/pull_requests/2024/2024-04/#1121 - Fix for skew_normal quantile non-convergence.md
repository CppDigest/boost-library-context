# #1121 Fix for skew_normal quantile non-convergence [Closed]

> Username: mborland  
> Created at: 2024-04-18 09:03:35 UTC  
> Updated at: 2024-06-28 18:15:44 UTC  
> Closed at: 2024-06-28 18:15:44 UTC  
> Url: https://github.com/boostorg/math/pull/1121  

@jzmaddock This may not be _the_ fix but it certainly gets things working. I just tested different shapes of the issue distribution to find decent places to make the change in allowable steps and how many we might need. Im not quite search how to narrow down the search range from (-inf, inf) to speed up convergence organically.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2024-06-28 18:07:13 UTC  
> Url: https://github.com/boostorg/math/pull/1121#issuecomment-2197412758  

This should have been fixed elsewhere now, can we close?

---
