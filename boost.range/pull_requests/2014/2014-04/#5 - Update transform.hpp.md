# #5 Update transform.hpp [Closed]

> Username: insideoutclub  
> Created at: 2014-04-27 16:51:51 UTC  
> Updated at: 2014-07-01 05:56:39 UTC  
> Closed at: 2014-04-28 16:52:26 UTC  
> Url: https://github.com/boostorg/range/pull/5  

Ticket #9812 BinaryOperation boost::transform only stops when it reaches the end of rng1, even if rng2 is shorter  
  
The docs for binary transform indicate that iteration should stop when either range reaches its end. However, the original implementation only stops when rng1 is finished, and asserts if rng2 reaches its end before rng1. I modified the transform to stop when either rng is exhausted. This behavior matches the existing documentation.  
  
This is my first pull request, so I'm learning. Please give constructive suggestions if there are parts of the process I'm missing or things I can do to make the process smoother.  
  
Thanks,  
David

---

## Comment 1

> Username: HighCommander4  
> Created_at: 2014-04-28 03:07:29 UTC  
> Url: https://github.com/boostorg/range/pull/5#issuecomment-41519683  

Thanks for the patch - it looks good!   
  
However, could you please make the pull request against the 'develop' branch rather than 'master'? Thanks!

---

## Comment 2

> Username: insideoutclub  
> Created_at: 2014-04-28 16:26:22 UTC  
> Url: https://github.com/boostorg/range/pull/5#issuecomment-41579885  

Done. I created a new pull request against the 'develop' branch.  
  
Thanks,  
David

---

## Comment 3

> Username: HighCommander4  
> Created_at: 2014-04-28 16:52:26 UTC  
> Url: https://github.com/boostorg/range/pull/5#issuecomment-41583162  

Thanks! I merged the new pull request. Closing this one.

---
