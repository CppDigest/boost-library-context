# #92 - Example tanh_sinh integrand broken [Closed]

> Username: NAThompson  
> Created at: 2017-11-04 19:58:01 UTC  
> Updated at: 2018-02-18 20:08:30 UTC  
> Closed at: 2018-02-18 20:08:30 UTC  
> Url: https://github.com/boostorg/math/issues/92  

In tanh_sinh quadrature, the example integrand  
  
```  
auto f = [](double x) { return log(x)*log(1-x); };  
```  
  
gets evaluated at a singularity by tanh_sinh. I am not sure that this is surprising due to the low precision of evaluating `log(1-x)` near `x=1`. If this is the case then the docs should be updated to reflect the fact that this integrand needs the "2-argument functor" case, rather than the default.  
  
It appears that `auto f = [](double x)->double { return sqrt(log(1/x)); };` also gets evaluated at a singular point by Apple LLVM version 9.0.0 (clang-900.0.38).

---

## Comment 1

> Username: NAThompson  
> Created at: 2018-02-18 20:08:30 UTC  
> Url: https://github.com/boostorg/math/issues/92#issuecomment-366543533  

Dunno what I was doing when I submitted this, but I can no longer reproduce. It works fine.
