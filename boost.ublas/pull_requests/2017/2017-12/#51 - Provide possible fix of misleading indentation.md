# #51 Provide possible fix of misleading indentation. [Closed]

> Username: mbradle  
> Created at: 2017-12-01 23:13:29 UTC  
> Updated at: 2017-12-01 23:17:41 UTC  
> Closed at: 2017-12-01 23:17:41 UTC  
> Url: https://github.com/boostorg/ublas/pull/51  

I get a warning of a misleading indentation when I compile odeint/examples/harmonic_oscillator.cpp with gcc version 7.2.0 and warning flag -Wall.  The warning goes away with the fix.  I'm not sure that the fix is correct, but it looks right and does make the if/then block for it2 more consistent with the if/then block for it1 immediately above it.

---

## Comment 1

> Username: mbradle  
> Created_at: 2017-12-01 23:17:41 UTC  
> Url: https://github.com/boostorg/ublas/pull/51#issuecomment-348639793  

Sorry, I missed that this is the same as pull request #50.  Closing my request.

---
