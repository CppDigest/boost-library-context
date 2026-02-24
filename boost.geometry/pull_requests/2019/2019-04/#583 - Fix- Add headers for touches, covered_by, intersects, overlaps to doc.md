# #583 Fix: Add headers for touches, covered_by, intersects, overlaps to doc [Merged]

> Username: tinko92  
> Created at: 2019-04-04 15:06:37 UTC  
> Updated at: 2019-04-08 01:23:52 UTC  
> Merged at: 2019-04-04 20:18:10 UTC  
> Closed at: 2019-04-04 20:18:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/583  

I'm sorry for the timing of this rather hastily put together pull request, however, I just now noticed that the documentation pages for covered_by, intersects, overlaps and touches are empty (since 1.65, in 1.64 they still had content) in the online documentation and the next release is already in the release candidate phase.  
  
See https://www.boost.org/doc/libs/1_70_0_beta1/libs/geometry/doc/html/geometry/reference/algorithms/touches.html for example.  
  
The result of this change is that some content shows up in the corresponding generated qbk files for these 4 algorithms which was not the case without the change (they had just 15 lines but no actual content), I'm not sure, though, whether this already fixes the issue. But with the release being so close I thought it might be more helpful to notify you in time with a possibly insufficient commit rather than trying to fully understand the documentation system.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2019-04-04 20:17:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/583#issuecomment-480047670  

Thanks! Yes this fixes the issue. I'm not sure if this will be released with 1.70 though.

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-04-04 20:25:11 UTC  
> Updated_at: 2019-04-04 20:27:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/583#issuecomment-480050248  

Good stuff, thanks!

---
