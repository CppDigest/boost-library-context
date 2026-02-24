# #12 Fix runtime error [Closed]

> Username: tinloaf  
> Created at: 2017-07-19 16:45:42 UTC  
> Updated at: 2017-07-19 16:46:31 UTC  
> Closed at: 2017-07-19 16:46:31 UTC  
> Url: https://github.com/boostorg/icl/pull/12  

This fixes undefined behavior, which is reported when compiled with -fsanitize=undefined.  
  
The ~ operator results in an integer promotion of _bits, with the result being negative. The following shift results in undefined behavior. Casting to bound_type solves this issue.  
  
I *think* I successfully ran all unit tests. The jamfile automatically executes them, right?

---

## Comment 1

> Username: tinloaf  
> Created_at: 2017-07-19 16:46:31 UTC  
> Url: https://github.com/boostorg/icl/pull/12#issuecomment-316447116  

D'Oh. I didn't see #11. That was stupid.

---
