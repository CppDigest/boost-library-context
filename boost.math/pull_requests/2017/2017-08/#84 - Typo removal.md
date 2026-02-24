# #84 Typo removal. [Merged]

> Username: NAThompson  
> Created at: 2017-08-23 20:07:12 UTC  
> Updated at: 2018-02-10 01:00:16 UTC  
> Merged at: 2017-08-24 09:47:48 UTC  
> Closed at: 2017-08-24 09:47:48 UTC  
> Url: https://github.com/boostorg/math/pull/84  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-08-24 09:50:59 UTC  
> Url: https://github.com/boostorg/math/pull/84#issuecomment-324589035  

Thanks for that, I've simplified the tanh_sinh::integrate declarations in the docs a bit - the rationale is that the whole decltype overload/metaprogramming stuff is an implementation detail - it could (maybe should?) have been hidden inside the integrate method, it was just easier to add it at the top level.  Also reformatted a bit for shorter line length / easier reading.

---
