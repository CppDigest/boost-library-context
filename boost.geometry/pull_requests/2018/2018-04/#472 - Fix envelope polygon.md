# #472 Fix envelope polygon [Merged]

> Username: vissarion  
> Created at: 2018-04-12 12:53:35 UTC  
> Updated at: 2018-04-12 14:16:25 UTC  
> Merged at: 2018-04-12 14:16:00 UTC  
> Closed at: 2018-04-12 14:16:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/472  

This PR fixes the bug reported on issue https://github.com/boostorg/geometry/issues/471.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2018-04-12 13:43:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/472#issuecomment-380809672  

I'm ok with merging, thanks.  
  
One remark regarding the implementation of envelope, not your fix but in general. Currently algorithm is dispatched with coordinate system. This is wrong, only strategies should depend on CS. So this is something which should be changed in the future.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2018-04-12 13:44:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/472#issuecomment-380809933  

Btw, you could add info in release notes about fixing the issue.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2018-04-12 14:15:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/472#issuecomment-380820681  

Thanks.

---
