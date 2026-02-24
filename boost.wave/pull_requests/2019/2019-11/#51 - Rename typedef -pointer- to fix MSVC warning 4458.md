# #51 Rename typedef "pointer" to fix MSVC warning 4458 [Merged]

> Username: poelmanc  
> Created at: 2019-11-22 18:32:41 UTC  
> Updated at: 2019-11-22 20:05:21 UTC  
> Merged at: 2019-11-22 20:05:21 UTC  
> Closed at: 2019-11-22 20:05:21 UTC  
> Url: https://github.com/boostorg/wave/pull/51  

MSVC emits warning 4458 about the local typedef "pointer" shadowing the class member typedef "pointer". This fix renames the local typedef to "value_pointer" to eliminate the warning.

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2019-11-22 20:05:13 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/51#pullrequestreview-321780919  

LGTM, thanks!

---
