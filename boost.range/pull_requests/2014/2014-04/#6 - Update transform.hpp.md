# #6 Update transform.hpp [Merged]

> Username: insideoutclub  
> Created at: 2014-04-28 16:25:15 UTC  
> Updated at: 2014-06-17 15:15:34 UTC  
> Merged at: 2014-04-28 16:51:07 UTC  
> Closed at: 2014-04-28 16:51:07 UTC  
> Url: https://github.com/boostorg/range/pull/6  

Ticket #9812 BinaryOperation boost::transform only stops when it reaches the end of rng1, even if rng2 is shorter  
  
The docs for binary transform indicate that iteration should stop when either range reaches its end. However, the original implementation only stops when rng1 is finished, and asserts if rng2 reaches its end before rng1. I modified the transform to stop when either rng is exhausted. This behavior matches the existing documentation.  
  
This is my first pull request, so I'm learning. Please give constructive suggestions if there are parts of the process I'm missing or things I can do to make the process smoother.  
  
Thanks,  
David

---
