# #315 ifdef declaration of scoped_trace [Closed]

> Username: andreasbuhr  
> Created at: 2026-02-15 18:45:34 UTC  
> Updated at: 2026-02-15 21:19:55 UTC  
> Closed at: 2026-02-15 21:19:54 UTC  
> Url: https://github.com/boostorg/parser/pull/315  

Can we keep the ifdefs around the declaration of scoped_trace?  
  
This assures that no usages of scoped_trace, not guarded by ifdefs, are added in the future.

---

## Comment 1

> Username: tzlaine  
> Created_at: 2026-02-15 21:19:54 UTC  
> Url: https://github.com/boostorg/parser/pull/315#issuecomment-3905204707  

That's not a bad idea, but I don't like to turn off large sections of code like that -- since it's yet another way of encouraging mistakes.  So, I updated the PR code with something equivalent, but using static_assert and early return instead.

---
