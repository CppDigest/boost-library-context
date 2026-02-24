# #96 Factor integer [Closed]

> Username: NAThompson  
> Created at: 2017-12-26 21:39:21 UTC  
> Updated at: 2024-01-24 21:32:31 UTC  
> Closed at: 2024-01-24 20:52:01 UTC  
> Url: https://github.com/boostorg/math/pull/96  

This is a first pass at integer factorization using the Pollard rho algorithm and trial division. These two algorithms should allow us to factor basically all 64 bit integers, which is more than sufficient for my purpose, which is to put the pieces in place to create an FFT.  
  
Even libraries implementing the GNFS (such as [msieve](https://sourceforge.net/projects/msieve/)) first use trial division and Pollard rho on all inputs before unleashing the GNFS on them, so IMO there is no shame in not providing a sieve at this juncture.  
  
Still a ways to go here, including replacing Floyd's cycle finding with Brent's cycle finding algorithm, using multiple iterates before computing the gcd, and increasing the usability by perhaps supporting cancellation. But this is a lot of fun, so look around and see if there's some nice improvements that can be made.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2018-12-04 21:39:06 UTC  
> Url: https://github.com/boostorg/math/pull/96#issuecomment-444268673  

I'm going to move this to boost integer; will close soon.

---
