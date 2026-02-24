# #544 Modify mersenne_twister_engine to copy its states. [Merged]

> Username: okdshin  
> Created at: 2015-12-26 10:27:09 UTC  
> Updated at: 2015-12-26 23:14:57 UTC  
> Merged at: 2015-12-26 23:13:59 UTC  
> Closed at: 2015-12-26 23:13:59 UTC  
> Url: https://github.com/boostorg/compute/pull/544  

The copy ctor and assign op of compute::mersenne_twister_engine does not copy its states. So copied engine causes "Invalid Program" error.  
This modification solves this problem.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-12-26 23:14:57 UTC  
> Url: https://github.com/boostorg/compute/pull/544#issuecomment-167370940  

Thanks for fixing this!  
  
It would probably be good to use the same pattern for the other random number engines.

---
