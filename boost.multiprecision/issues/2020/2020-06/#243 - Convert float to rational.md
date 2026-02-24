# #243 - Convert float to rational? [Closed]

> Username: NAThompson  
> Created at: 2020-06-04 01:45:45 UTC  
> Updated at: 2020-06-15 17:40:03 UTC  
> Closed at: 2020-06-15 17:40:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/243  

Can `rational_adaptor` be used to convert a floating point number into a rational number? I note that in MPFR, we have the function [mpfr_get_q](https://www.mpfr.org/mpfr-4.0.0/) which can perform the conversion, but I don't see this wrapped, nor can I see quite how it fits into the Boost.Multiprecision ecosystem.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-06-04 09:30:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/243#issuecomment-638735252  

Yes, all rational numbers are exactly convertible from floats.

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-06-04 12:59:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/243#issuecomment-638831185  

Forgive me, I mean is there currently a way to do this or does it need to be implemented?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-06-04 14:04:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/243#issuecomment-638870526  

Yes, just construct or assign a rational from a float, and it'll do the exact conversion.  The actual code is in generic_interconvert_float2rational, generic_nterconvert.hpp:397.

---

## Comment 4

> Username: NAThompson  
> Created at: 2020-06-14 20:17:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/243#issuecomment-643816840  

Ok, I'm doing something silly then, since both of these fail for me:  
  
```cpp  
#include <boost/multiprecision/mpfr.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/rational_adaptor.hpp>  
#include <boost/multiprecision/cpp_int.hpp>  
  
using boost::multiprecision::cpp_bin_float_100;  
using boost::multiprecision::mpfr_float;  
  
int main() {  
    //cpp_bin_float_100 x{"3.14159"};  
    //boost::multiprecision::cpp_rational r = x;  
  
    mpfr_float y = 3.14159;  
    boost::multiprecision::mpq_rational q = y;  
}  
```  
  
The error message is:  
  
```  
try_to_rational.cpp:14:45: error: conversion from 'number<boost::multiprecision::backends::mpfr_float_backend<0, boost::multiprecision::allocate_dynamic>>' to non-scalar type 'number<boost::multiprecision::backends::gmp_rational>' requested  
   14 |     boost::multiprecision::mpq_rational q = y;  
      |                                             ^  
```

---

## Comment 5

> Username: jzmaddock  
> Created at: 2020-06-14 21:17:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/243#issuecomment-643823422  

I must have goofed somewhere because it works for cpp_rational, but not mpq_rational :(

---

## Comment 6

> Username: NAThompson  
> Created at: 2020-06-14 23:32:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/243#issuecomment-643837431  

@jzmaddock : I'm also getting an error with `cpp_rational`:  
  
```  
make main.x  
g++-9 -g --std=gnu++17 -ffast-math -fno-finite-math-only -march=native -Wfatal-errors -MMD -fvisibility=hidden -O3 -I../../ -I/usr/local/include/eigen3 -I/usr/local/include -o main.x main.cpp -pthread -lbenchmark -lbenchmark_main -L/usr/local/lib -lfftw3f -lfftw3 -lmpfr  
main.cpp: In function 'int main()':  
main.cpp:11:45: error: conversion from 'number<boost::multiprecision::backends::cpp_bin_float<100>>' to non-scalar type 'number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> >>' requested  
   11 |     boost::multiprecision::cpp_rational r = x;  
      |                                             ^  
compilation terminated due to -Wfatal-errors.  
```

---

## Comment 7

> Username: jzmaddock  
> Created at: 2020-06-15 09:15:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/243#issuecomment-644008514  

My bad, I posted in a hurry last night, the constructor is _explicit_ so you need:  
  
```  
    mpfr_float y = 3.14159;  
    boost::multiprecision::mpq_rational q(y);  
```

---

## Comment 8

> Username: NAThompson  
> Created at: 2020-06-15 14:03:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/243#issuecomment-644154895  

@jzmaddock : Awesome, that worked.  
  
Would it be sensible to add an assignment operator?

---

## Comment 9

> Username: jzmaddock  
> Created at: 2020-06-15 16:43:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/243#issuecomment-644245513  

>Would it be sensible to add an assignment operator?  
  
Haha, well that wouldn't be an explicit conversion then!  
  
You need:  
```  
q = static_cast<mpq_rational>(my_float);  
```
