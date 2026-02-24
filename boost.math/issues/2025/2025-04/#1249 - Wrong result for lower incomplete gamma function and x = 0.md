# #1249 - Wrong result for lower incomplete gamma function and x = 0 [Closed]

> Username: cohomology  
> Created at: 2025-04-01 15:44:23 UTC  
> Updated at: 2025-05-23 17:27:55 UTC  
> Closed at: 2025-05-23 17:27:55 UTC  
> Url: https://github.com/boostorg/math/issues/1249  

Computation of tgamma_lower or any other incomplete gamma function yields wrong results for x = 0.  
  
I am directly referring to [special_functions/gamma.hpp](https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/gamma.hpp), function `gamma_incomplete_imp_final` and for simplicity we compute with type `T = double`.  
  
When trying to compute the lower incomplete gamma function with   
a = 1 and x = 0, the check:  
  
```  
   if(a <= 0)  
      return policies::raise_domain_error<T>(function, "Argument a to the incomplete gamma function must be greater than zero (got a=%1%).", a, pol);  
   if(x < 0)  
      return policies::raise_domain_error<T>(function, "Argument x to the incomplete gamma function must be >= 0 (got x=%1%).", x, pol);  
```  
  
lets everything through and the computation continues.  
  
Later we have the following check:  
```  
    if(T(-0.4) / log(x) < a)  
      {  
         eval_method = 2;  
      }  
      else  
      {  
         eval_method = 3;  
      }  
```  
This sets errno to EDOM and eval_method to 2, as log(0) is -Inf.  
  
Now the computation continues and continues, but errno is never set back. Thus the caller, when he chose the ERRNO error propagation method, gets the wrong result overflow, where the result should be 0.

---

## Comment 1

> Username: cohomology  
> Created at: 2025-04-01 17:52:54 UTC  
> Url: https://github.com/boostorg/math/issues/1249#issuecomment-2770252973  

I am working on a fix

---

## Comment 2

> Username: cohomology  
> Created at: 2025-04-02 07:57:48 UTC  
> Url: https://github.com/boostorg/math/issues/1249#issuecomment-2771650890  

What I am wondering is that there are many instances of log(x) or 1 / x in gamma.hpp and generally no checks for x != 0 anywhere. Just happily applied. Perhaps worth to check them all at some point.
