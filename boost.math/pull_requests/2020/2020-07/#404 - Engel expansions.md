# #404 Engel expansions [Merged]

> Username: NAThompson  
> Created at: 2020-07-17 19:36:27 UTC  
> Updated at: 2020-07-20 13:51:24 UTC  
> Merged at: 2020-07-20 13:51:18 UTC  
> Closed at: 2020-07-20 13:51:18 UTC  
> Url: https://github.com/boostorg/math/pull/404  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-07-18 18:20:51 UTC  
> Url: https://github.com/boostorg/math/pull/404#issuecomment-660521386  

That looks like fun ;)  Just out of curiosity do you have a use case in mind?  
  
It all looks fine BTW.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2020-07-18 21:26:14 UTC  
> Url: https://github.com/boostorg/math/pull/404#issuecomment-660544790  

The goal is to have a large number of ways to represent each number; so we now have simple continued fractions, centered continued fractions, Luroth expansions, and now Engel expansions. This allows us to decide is a number is "interesting" in some way; for example exp(1) has a very simple and unusual Engel expansion, whereas the Engel expansion of pi looks "random" (albeit with exponential growth of terms).  
  
I'm using these utilities in the PSLQ PR; see [here](https://github.com/boostorg/multiprecision/blob/3711b54b62465c425489e68f18599bcacbfb3dfb/example/representations/dottie.cpp) for an example.

---
