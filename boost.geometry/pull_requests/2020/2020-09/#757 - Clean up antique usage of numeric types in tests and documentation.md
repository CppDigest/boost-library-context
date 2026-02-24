# #757 Clean up antique usage of numeric types in tests and documentation [Merged]

> Username: barendgehrels  
> Created at: 2020-09-30 09:23:08 UTC  
> Updated at: 2020-10-15 23:38:56 UTC  
> Merged at: 2020-10-07 08:09:26 UTC  
> Closed at: 2020-10-07 08:09:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/757  

Because I tested with Boost.Multiprecision, I did a small follow-up to clean up all the old types. I found also an old piece of documentation which is now updated for usage with MP.  
  
If you agree I will also remove `ttmath` from extensions/contrib because it is never used anymore (as far as I know).  
  
Also I added tests for Boost.Multiprecision in tests for union and buffer (one). Note that expectations are not updated (except for union.cpp), they are not run by default. We can update the expectations if rescaling is removed - but it runs quite well with them, now also for buffer.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2020-09-30 09:43:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/757#pullrequestreview-499258567  

I am ok to remove mathtt, also gmp. For the latter there is support in boost mp. I am also using boost mp in https://github.com/boostorg/geometry/pull/756

---
