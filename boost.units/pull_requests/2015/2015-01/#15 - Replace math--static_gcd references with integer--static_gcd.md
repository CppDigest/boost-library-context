# #15 Replace math::static_gcd references with integer::static_gcd [Merged]

> Username: pdimov  
> Created at: 2015-01-18 17:36:26 UTC  
> Updated at: 2015-01-19 15:06:38 UTC  
> Merged at: 2015-01-19 15:06:29 UTC  
> Closed at: 2015-01-19 15:06:29 UTC  
> Url: https://github.com/boostorg/units/pull/15  



---

## Comment 1

> Username: jhunold  
> Created_at: 2015-01-19 09:40:57 UTC  
> Url: https://github.com/boostorg/units/pull/15#issuecomment-70467252  

The tests don't pass.  
  
```  
../../../boost/multiprecision/cpp_int/misc.hpp:508:22: error: ‘gcd’ is not a member of ‘boost::math’  
    *result.limbs() = boost::math::gcd(*a.limbs(), *b.limbs());  
```  
  
from `test_information_units.cpp`.   
It seems that Boost.Multiprecision needs to be patched first.

---

## Comment 2

> Username: jhunold  
> Created_at: 2015-01-19 10:08:19 UTC  
> Url: https://github.com/boostorg/units/pull/15#issuecomment-70470497  

This is already broken in current develop.  
Created https://github.com/boostorg/multiprecision/pull/7 for this.

---

## Comment 3

> Username: pdimov  
> Created_at: 2015-01-19 14:41:42 UTC  
> Url: https://github.com/boostorg/units/pull/15#issuecomment-70502843  

Multiprecision broke because it uses math::gcd without including the header. math::gcd is still available in Math, but the Math header is no longer included by Rational.

---
