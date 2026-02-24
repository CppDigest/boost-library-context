# #26 - Improve nth_element() algorithm performance [Closed]

> Username: kylelutz  
> Created at: 2013-12-21 19:34:50 UTC  
> Updated at: 2014-08-12 04:45:21 UTC  
> Closed at: 2014-08-12 04:45:21 UTC  
> Url: https://github.com/boostorg/compute/issues/26  

Right now, `nth_element()` is trivially implemented with a call to `sort()`. This should be improved.  
  
Look up the radix select algorithm for one possible improvement.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-08-12 04:45:21 UTC  
> Url: https://github.com/boostorg/compute/issues/26#issuecomment-51873165  

Implemented in PR #208
