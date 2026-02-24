# #175 Condition numbers [Closed]

> Username: NAThompson  
> Created at: 2019-01-10 01:45:57 UTC  
> Updated at: 2019-03-03 00:14:46 UTC  
> Closed at: 2019-01-28 18:28:45 UTC  
> Url: https://github.com/boostorg/math/pull/175  

Currently, this uses the finite difference derivative, which works well. But one Mike Pulver finishes the automatic differentiation, we can swap it out and get a very nice calculation of the condition number of function evaluation.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2019-01-28 18:28:38 UTC  
> Url: https://github.com/boostorg/math/pull/175#issuecomment-458246578  

Actually, I now think that using automatic differentiation for this is overkill; a condition number correct to 1 digit is all that is necessary, so the more robust finite differencing should work just fine.

---
