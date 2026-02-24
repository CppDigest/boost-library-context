# #1171 - Code question: "argument reduction" in Cauchy `quantile_imp` [Closed]

> Username: WarrenWeckesser  
> Created at: 2024-08-08 00:14:25 UTC  
> Updated at: 2024-08-10 17:43:55 UTC  
> Closed at: 2024-08-10 17:43:55 UTC  
> Url: https://github.com/boostorg/math/issues/1171  

At this line  
  
https://github.com/boostorg/math/blob/ab09ecec1d4cee65868951aa8dfe5ae4a1e4fa21/include/boost/math/distributions/cauchy.hpp#L147  
  
`p` has already been validated, and the end points have been handled, so 0 < p < 1.  Is that line necessary?  
  
This came up in a [scipy pull request](https://github.com/scipy/scipy/pull/21335).

---

## Comment 1

> Username: WarrenWeckesser  
> Created at: 2024-08-08 14:47:00 UTC  
> Updated at: 2024-08-08 20:42:27 UTC  
> Url: https://github.com/boostorg/math/issues/1171#issuecomment-2276015893  

FWIW: If I change that line to just `RealType P = p;`, the Boost/math tests all pass.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-08-10 14:19:38 UTC  
> Url: https://github.com/boostorg/math/issues/1171#issuecomment-2282031516  

You're quite correct, not sure what was going on there :(  
  
Will fix.
