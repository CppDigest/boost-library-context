# #105 - Cannot overload operator>> for safe_numerics::safe [Closed]

> Username: Eelis  
> Created at: 2021-03-30 22:18:56 UTC  
> Updated at: 2021-05-24 18:03:15 UTC  
> Closed at: 2021-05-23 22:06:50 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/105  

```  
#include <boost/safe_numerics/safe_integer.hpp>  
struct X {};  
using I = boost::safe_numerics::safe<int64_t>;  
void operator>>(X, I);  
void f(X x, I i) { x >> i; } // causes error deep inside SafeNumerics headers  
```  
https://godbolt.org/z/eozM1PqW8

---

## Comment 1

> Username: Eelis  
> Created at: 2021-04-03 17:33:10 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/105#issuecomment-812898425  

I think the cause is that there's a bad catchall operator>> for `safe_base` that only disables itself if the lhs is derived from std::ios_base:  
https://github.com/boostorg/safe_numerics/blob/e15c54c3e2d69e644d164ac51551f48e69a65eff/include/boost/safe_numerics/safe_base_operations.hpp#L1394-L1400

---

## Comment 2

> Username: robertramey  
> Created at: 2021-04-03 21:13:43 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/105#issuecomment-812926278  

Your correct.  I'm working on fixing it in a definitive way as we "speak".

---

## Comment 3

> Username: robertramey  
> Created at: 2021-05-18 22:39:51 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/105#issuecomment-843613988  

This should be fixed by now on the develop branch. Also added a test for it so it doesn't come back.  We'll leave this message until a 2nd person has confirmed it.

---

## Comment 4

> Username: robertramey  
> Created at: 2021-05-23 22:07:05 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/105#issuecomment-846631138  

closed/fixed

---

## Comment 5

> Username: Eelis  
> Created at: 2021-05-24 18:03:15 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/105#issuecomment-847230732  

Sweet, thanks!!
