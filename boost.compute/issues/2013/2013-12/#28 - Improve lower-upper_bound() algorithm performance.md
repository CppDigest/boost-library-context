# #28 - Improve lower/upper_bound() algorithm performance [Closed]

> Username: kylelutz  
> Created at: 2013-12-21 19:35:36 UTC  
> Updated at: 2014-08-12 04:47:34 UTC  
> Closed at: 2014-08-12 04:47:34 UTC  
> Url: https://github.com/boostorg/compute/issues/28  

Currently the binary search algorithms are trivially implemented with `find_if()`. This can be much improved by taking advantage of the sorted inputs.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-08-12 04:47:34 UTC  
> Url: https://github.com/boostorg/compute/issues/28#issuecomment-51873263  

Implemented in PR #195
