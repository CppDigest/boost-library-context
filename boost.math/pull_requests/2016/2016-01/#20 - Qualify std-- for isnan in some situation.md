# #20 Qualify std:: for isnan in some situation. [Merged]

> Username: Flast  
> Created at: 2016-01-06 22:47:42 UTC  
> Updated at: 2016-01-10 06:00:24 UTC  
> Merged at: 2016-01-09 19:33:20 UTC  
> Closed at: 2016-01-09 19:33:20 UTC  
> Url: https://github.com/boostorg/math/pull/20  

Because isnan is implemented as a macro and libstdc++ undef it within <cmath> (at least FreeBSD 10).  
  
See `Flast-FreeBSD10-gcc-4.8.5~gnu++11` column on http://www.boost.org/development/tests/develop/developer/math.html (e.g. [bessel_zeros_example_1](http://www.boost.org/development/tests/develop/developer/output/Flast-FreeBSD10-gcc-4-8-5~gnu++11-boost-bin-v2-libs-math-example-bessel_zeros_example_1-test-gcc-4-8-5-debug.html)).

---
