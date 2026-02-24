# #330 Fix assertion failure in distance(Pt, Box) for NaN coordinates [Merged]

> Username: awulkiew  
> Created at: 2015-10-08 15:59:29 UTC  
> Updated at: 2015-10-09 13:56:07 UTC  
> Merged at: 2015-10-08 21:43:58 UTC  
> Closed at: 2015-10-08 21:43:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/330  

If there are NaN coordinates the assertions in distance(Pt, Box) cannot be reasonably checked, therefore this PR changes the conditions so if the original condition isn't met an additional check is performed if NaN coordinates were not involved. This is consistent with the general policy WRT invalid geometries, the algorithm doesn't fail but may generate invalid result.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-10-08 16:51:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/330#issuecomment-146619573  

Looks good to me.  
I am in favor of merging.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-10-08 21:19:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/330#issuecomment-146689065  

I'm OK too

---
