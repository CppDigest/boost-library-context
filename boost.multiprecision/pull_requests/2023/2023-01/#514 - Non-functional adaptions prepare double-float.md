# #514 Non-functional adaptions prepare double-float [Merged]

> Username: ckormanyos  
> Created at: 2023-01-03 09:23:23 UTC  
> Updated at: 2023-01-03 11:58:02 UTC  
> Merged at: 2023-01-03 11:56:31 UTC  
> Closed at: 2023-01-03 11:56:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/514  

The purpose of this pull request is to  handle some non-functional test-adaptions that I found necessary while preparing the `double-float` work.  
  
The big change to the arithmetic tests handles a case of the `pow()` function when low digit counts are being tested. It only makes sense to test the digits that are actually present in the type.  
  
The change in the `sqrt()` test includes explicit check of `sqrt(0) == 0`, which should be no problem.  
  
Then I made a few casts that should be correct but hopefully meaningless regarding functionality.  
  
Cc: @jzmaddock and @mborland

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2023-01-03 11:56:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/514#issuecomment-1369684952  

I'll go ahead and merge this one.  
The non-functional adaptions both pave the way for future work, as well as make sense.

---
