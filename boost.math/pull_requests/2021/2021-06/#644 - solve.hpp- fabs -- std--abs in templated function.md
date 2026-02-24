# #644 solve.hpp: fabs -> std::abs in templated function [Merged]

> Username: jwuttke  
> Created at: 2021-06-19 16:52:49 UTC  
> Updated at: 2021-06-19 22:58:00 UTC  
> Merged at: 2021-06-19 22:57:51 UTC  
> Closed at: 2021-06-19 22:57:51 UTC  
> Url: https://github.com/boostorg/math/pull/644  

correction needed to make boost::math::tools::solve<T> work with T=long double

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-06-19 17:00:55 UTC  
> Url: https://github.com/boostorg/math/pull/644#issuecomment-864436448  

Maybe should be:  
  
```cpp  
using std::abs;  
T err = abs(delta[i] / b[i]);  
```  
  
so it works with quad/multiprecision as well?

---

## Comment 2

> Username: NAThompson  
> Created_at: 2021-06-19 17:05:17 UTC  
> Url: https://github.com/boostorg/math/pull/644#issuecomment-864436958  

Looks good to me. @jzmaddock ?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-06-19 17:41:01 UTC  
> Url: https://github.com/boostorg/math/pull/644#issuecomment-864441136  

That's fine, thought probably just needed a `using std::fabs` ;)

---

## Comment 4

> Username: NAThompson  
> Created_at: 2021-06-19 22:58:00 UTC  
> Url: https://github.com/boostorg/math/pull/644#issuecomment-864473549  

Build failure was a timeout.

---
