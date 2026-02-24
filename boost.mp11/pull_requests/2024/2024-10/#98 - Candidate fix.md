# #98 Candidate fix [Merged]

> Username: tjcw  
> Created at: 2024-10-22 08:02:16 UTC  
> Updated at: 2024-10-22 16:42:14 UTC  
> Merged at: 2024-10-22 16:40:18 UTC  
> Closed at: 2024-10-22 16:40:18 UTC  
> Url: https://github.com/boostorg/mp11/pull/98  

See issue #97 . Workaround for Windows compiler bug.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-10-22 16:42:12 UTC  
> Url: https://github.com/boostorg/mp11/pull/98#issuecomment-2429766538  

Interestingly, the code was originally of this form, and was changed to iterate from 1 in this commit  
  
https://github.com/boostorg/mp11/commit/6fdfb5bde8552b1779224480818e2667e88836b2  
  
to avoid a GCC 10 warning.  
  
However, today's CI says that there's nothing wrong with GCC 10 anymore, and I see in the issue that you've tested with GCC 10 as well without problems.  
  
I'll also change the second occurrence of the same loop to match.

---
