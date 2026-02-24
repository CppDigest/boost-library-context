# #22 Fix default build to release variant. [Merged]

> Username: grafikrobot  
> Created at: 2025-05-07 02:56:43 UTC  
> Updated at: 2025-05-07 03:18:12 UTC  
> Merged at: 2025-05-07 03:10:40 UTC  
> Closed at: 2025-05-07 03:10:40 UTC  
> Url: https://github.com/boostorg/inspect/pull/22  

Again, moving the default build to the project prevents a default debug variant being picked in some circumstances as the first default encountered.

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-05-07 03:11:06 UTC  
> Url: https://github.com/boostorg/inspect/pull/22#issuecomment-2856889209  

I suppose we need the same thing for `tools/quickbook` as well?

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2025-05-07 03:18:11 UTC  
> Url: https://github.com/boostorg/inspect/pull/22#issuecomment-2856896551  

> I suppose we need the same thing for `tools/quickbook` as well?  
  
Yes. And repeat for the other tools.

---
