# #258 - Setting precision in gmp_int is undocumented. [Closed]

> Username: NAThompson  
> Created at: 2020-07-18 14:11:53 UTC  
> Updated at: 2020-12-26 16:16:03 UTC  
> Closed at: 2020-12-26 16:16:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/258  

See [here](https://www.boost.org/doc/libs/1_73_0/libs/multiprecision/doc/html/boost_multiprecision/tut/ints/gmp_int.html).

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-07-18 14:58:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/258#issuecomment-660495484  

?  You don't/can't set it's precision, it's a variable/infinite precision type.

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-07-18 15:11:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/258#issuecomment-660496997  

@jzmaddock : Then I'm a bit confused about its behavior; it saturated on conversion from `mpfr` types to the `int64_t` max. In any case, I can't reproduce since now I'm hitting the following:  
  
```  
#include <boost/multiprecision/mpfr.hpp>  
#include <boost/multiprecision/gmp.hpp>  
  
int main() {  
    using boost::multiprecision::gmp_int;  
      
    using boost::multiprecision::mpfr_float;  
    mpfr_float::default_precision(1024);  
    mpfr_float four = 4;  
    gmp_int g = static_cast<gmp_int>(four);  
}  
```  
  
Is this expected to work?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-07-18 17:29:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/258#issuecomment-660514911  

gmp_int is the backend type: change it to mpz_int and it does work ;)
