# #213 Update second order root finding code to bracket root when required. [Merged]

> Username: jzmaddock  
> Created at: 2019-05-25 17:18:51 UTC  
> Updated at: 2019-10-17 21:46:51 UTC  
> Merged at: 2019-06-23 14:04:57 UTC  
> Closed at: 2019-06-23 14:04:57 UTC  
> Url: https://github.com/boostorg/math/pull/213  

When min and max differ by many orders of magnitude (as happens when one is  
zero for example), then bisection can take a very long time to iterate down to  
the root.  Instead use a bracketing strategy which doubles the step size with  
each iteration until a bracket is found, then repeat recursively as required  
until we have a reasonably small interval.  Note that this only kicks in when  
a Halley step goes out of bounds and we're therefore forced to thrash around  
looking for the root.  Fixes: https://github.com/boostorg/math/issues/204.

---
