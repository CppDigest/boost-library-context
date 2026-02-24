# #319 - Generic operation (sqrt) on integer expression [Closed]

> Username: mglisse  
> Created at: 2021-04-07 12:32:24 UTC  
> Updated at: 2021-04-09 09:08:15 UTC  
> Closed at: 2021-04-09 09:08:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/319  

It looks like one cannot call `sqrt` on an integer expression.  
```c++  
#include <boost/multiprecision/gmp.hpp>  
typedef boost::multiprecision::mpz_int T;  
T f(T t){  
  return boost::multiprecision::sqrt(t+t);  
}  
```  
> m.cc:4:41: error: no matching function for call to ‘sqrt(boost::multiprecision::detail::expression<boost::multiprecision::detail::add_immediates, boost::multiprecision::number<boost::multiprecision::backends::gmp_int>, boost::multiprecision::number<boost::multiprecision::backends::gmp_int>, void, void>)’  
  
The documentation https://www.boost.org/doc/libs/1_75_0/libs/multiprecision/doc/html/boost_multiprecision/tut/gen_int.html indeed seems to use `Integer` here, while it uses `number-or-expression-template-type` for `miller_rabin_test` so that may be on purpose, and it makes perfect sense for functions that take a non-const reference, but it would be nice to allow it in this case.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-04-08 16:07:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/319#issuecomment-815950941  

Thanks for the heads up: it's an oversight with (hopefully) an easy fix in https://github.com/boostorg/multiprecision/pull/320
