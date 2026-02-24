# #375 - Document search range for includes() algorithm [Closed]

> Username: kylelutz  
> Created at: 2014-12-29 18:59:10 UTC  
> Updated at: 2014-12-30 21:40:49 UTC  
> Closed at: 2014-12-30 21:40:49 UTC  
> Url: https://github.com/boostorg/compute/issues/375  

From the Boost.Compute review:  
  
"http://kylelutz.github.io/compute/boost/compute/includes.html it's unclear whether the subrange has to be contiguous."  
  
We should update the `includes()` algorithm documentation to specific if there are any requirements for the search range. Also, having a small example doctest could be useful for demonstrating the algorithm.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-12-30 00:36:58 UTC  
> Url: https://github.com/boostorg/compute/issues/375#issuecomment-68319321  

@roshanr95 can you take care of this one?

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-12-30 21:40:46 UTC  
> Url: https://github.com/boostorg/compute/issues/375#issuecomment-68401574  

Fixed in PR #395.
