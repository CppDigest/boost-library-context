# #70 Fix more variable precision issues: [Merged]

> Username: jzmaddock  
> Created at: 2018-08-16 18:22:16 UTC  
> Updated at: 2019-11-02 11:28:24 UTC  
> Merged at: 2018-08-17 08:10:12 UTC  
> Closed at: 2018-08-17 08:10:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/70  

Make sure that temporaries use the same precision as the target.  
Make sure that construction from mpfr_float's copies their precision.

---
