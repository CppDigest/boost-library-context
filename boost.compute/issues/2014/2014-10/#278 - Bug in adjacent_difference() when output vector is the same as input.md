# #278 - Bug in adjacent_difference() when output vector is the same as input [Closed]

> Username: pentschev  
> Created at: 2014-10-16 13:45:15 UTC  
> Updated at: 2014-10-23 03:14:23 UTC  
> Closed at: 2014-10-23 03:14:23 UTC  
> Url: https://github.com/boostorg/compute/issues/278  

The `adjacent_difference()` may give wrong results for the first element in each work group. This occurs because the previous vector element might be already changed when the work group starts.  
  
One possible solution is to make a temporary copy of the input vector before processing it, thus always reading non-modified data.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-10-23 03:14:23 UTC  
> Url: https://github.com/boostorg/compute/issues/278#issuecomment-60187570  

Fixed in PR #279.
