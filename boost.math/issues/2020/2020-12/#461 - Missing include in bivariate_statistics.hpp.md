# #461 - Missing include in bivariate_statistics.hpp [Closed]

> Username: dgoulet  
> Created at: 2020-12-21 14:53:24 UTC  
> Updated at: 2021-01-18 15:04:25 UTC  
> Closed at: 2021-01-18 15:04:25 UTC  
> Url: https://github.com/boostorg/math/issues/461  

Greetings,  
  
I just updated to 1.74 and one of my project is getting this warning:  
  
```  
/usr/include/boost/math/statistics/bivariate_statistics.hpp:85:24: error: ‘sqrt’ was not declared in this scope, and no declarations were found by argument-dependent lookup at the point of instantiation [-fpermissive]  
   85 |     Real rho = cov/sqrt(Qu*Qv);  
```  
  
It appears the header is simply missing `#include <cmath>` which fixes it for me. Let me know if you would like a PR for this. I figured something that trivial is likely something you can push quickly.  
  
Cheers!

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-12-21 16:01:08 UTC  
> Url: https://github.com/boostorg/math/issues/461#issuecomment-749048639  

Sort of, it's missing a using std::sqrt as well.... otherwise it will call `::sqrt(double)` which may lose precision (long double, __float128 etc).  
  
@NAThompson do we have a #include test for this, plus a concept check?

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-12-23 23:28:45 UTC  
> Url: https://github.com/boostorg/math/issues/461#issuecomment-750561489  

@jzmaddock : We do not; my bad!

---

## Comment 3

> Username: mborland  
> Created at: 2021-01-07 16:34:19 UTC  
> Url: https://github.com/boostorg/math/issues/461#issuecomment-756227448  

I believe this was resolved in PR #480.

---

## Comment 4

> Username: NAThompson  
> Created at: 2021-01-17 14:49:52 UTC  
> Url: https://github.com/boostorg/math/issues/461#issuecomment-761824321  

@dgoulet : Is this resolved on your end?

---

## Comment 5

> Username: NAThompson  
> Created at: 2021-01-18 15:04:18 UTC  
> Url: https://github.com/boostorg/math/issues/461#issuecomment-762305743  

Assuming resolved; feel free to reopen if not.
