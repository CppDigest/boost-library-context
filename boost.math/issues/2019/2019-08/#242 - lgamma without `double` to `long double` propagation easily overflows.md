# #242 - lgamma without `double` to `long double` propagation easily overflows [Closed]

> Username: wds15  
> Created at: 2019-08-13 06:26:34 UTC  
> Updated at: 2019-08-13 20:49:38 UTC  
> Closed at: 2019-08-13 20:48:58 UTC  
> Url: https://github.com/boostorg/math/issues/242  

When disabling the `double` to `long double` propagation of the `boost::math::lgamma` then the function is a lot faster (comparable to `std::lgamma`)... but the boost version now overflows easily for large arguments at around 1E25 (still ok) while 1E27 leads to NaN. The `std::lgamma` implementation returns results up to 1E300 which is close to the maximal `double` representable. In fact, the `boost::math::lgamma` implementation is also able to return results for these large arguments, but only if the `double` to `long double` propagation is used.  
  
Quickly inspecting the code of `lgamma` shows that most of the computation is done on the natural scale instead of the log scale which seems to be the source of the problem.  
  
I would expect the boost `lgamma` to return correct results for the same support as `std::lgamma` even if `double` to `long double` propagation is turned on.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-08-13 09:16:19 UTC  
> Url: https://github.com/boostorg/math/issues/242#issuecomment-520757453  

This should definitely work - we do try really hard not to get spurious over/underflow - I'll look into it shortly.

---

## Comment 2

> Username: wds15  
> Created at: 2019-08-13 09:27:21 UTC  
> Url: https://github.com/boostorg/math/issues/242#issuecomment-520761190  

Thanks!

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-08-13 10:49:06 UTC  
> Url: https://github.com/boostorg/math/issues/242#issuecomment-520786011  

Well ironically.... this turns out to be an optimisation issue, if you comment out:  
  
```  
#include <boost/math/special_functions/detail/lanczos_sse2.hpp>  
```  
  
In lanczos.hpp then everything is fine.  
  
Unfortunately the x64/SSE2 optimised code fails when z > sqrt(DOUBLE_MAX), I need to rethink that.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2019-08-13 20:49:38 UTC  
> Url: https://github.com/boostorg/math/issues/242#issuecomment-521003525  

Please see commit above for the fix.
