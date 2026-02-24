# #24 Fix move assignment for child_decl [Merged]

> Username: ca-l-eb  
> Created at: 2017-11-02 23:08:49 UTC  
> Updated at: 2017-11-02 23:17:15 UTC  
> Merged at: 2017-11-02 23:13:18 UTC  
> Closed at: 2017-11-02 23:13:18 UTC  
> Url: https://github.com/boostorg/process/pull/24  

_terminate field is now copied from the move assingment operator on  
child. This fixes the issue that a child process could previously only  
be terminated() once (even after reassignment).

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2017-11-02 23:13:25 UTC  
> Url: https://github.com/boostorg/process/pull/24#issuecomment-341585375  

My bad, thanks!

---
