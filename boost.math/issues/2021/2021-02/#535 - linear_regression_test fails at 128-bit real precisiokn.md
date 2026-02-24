# #535 - linear_regression_test fails at 128-bit real precisiokn [Open]

> Username: jzmaddock  
> Created at: 2021-02-13 18:31:21 UTC  
> Updated at: 2021-02-21 11:26:10 UTC  
> Url: https://github.com/boostorg/math/issues/535  

@NAThompson : I'm seeing:  
  
```  
Error at libs/math/test/linear_regression_test.cpp:test_permutation_invariance:167:  
  Mollified relative error in __float128 precision is 0.00238, which exceeds 0.002, error/tol  = 1.19.  
  Expected: -7.200000000000000177635683940025046468 = -0x1.ccccccccccccdp+2  
  Computed: -7.217158929845008151987144669539711967 = -0x1.cde5ee916dfea99579fa451efbfep+2  
  ULP distance: +22273564382513600491790226357246  
Error at libs/math/test/linear_regression_test.cpp:test_scaling_relations:211:  
  Mollified relative error in __float128 precision is 0.00536, which exceeds 0.005, error/tol  = 1.07.  
  Expected: +3.200000000000000177635683940025046468 = +0x1.999999999999ap+1  
  Computed: +3.182841070154992203284223210510388672 = +0x1.976756105735ecd50c0b75c20818p+1  
  ULP distance: +44547128765027200983580452714472  
Error count: 2  
```  
  
from linear_regression_test.cpp on Linux/s390x.  
  
Are the existing error bounds arbitrary?  They only just fail, so it's temping to just them a bit.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-02-13 19:00:03 UTC  
> Url: https://github.com/boostorg/math/issues/535#issuecomment-778662698  

A related issue for the whittaker shannon test:  
  
```  
Error at libs/math/test/whittaker_shannon_test.cpp:test_bump:117:  
  Mollified relative error in __float128 precision is 2.05e-29, which exceeds 1.93e-33, error/tol  = 1.07e+04.  
  Expected: +0.360803088275448657029385009164666528 = +0x1.71765d61c162afbf43d7d05ea90ap-2  
  Computed: +0.360803088275448657029385009185199204 = +0x1.71765d61c162afbf43d7d0652ad9p-2  
  ULP distance: +426447  
  Problem occurred at abscissa 0.138032129403380054031217804789231206  
```  
  
and lots more the same.

---

## Comment 2

> Username: NAThompson  
> Created at: 2021-02-13 19:15:20 UTC  
> Url: https://github.com/boostorg/math/issues/535#issuecomment-778664664  

@jzmaddock : Wowsers the linear regression test looks bad. The Whittaker-Shannon also looks like it's beyond reasonable "fudge the tolerance bounds." Will try to look into this soon.

---

## Comment 3

> Username: mborland  
> Created at: 2021-02-20 13:40:43 UTC  
> Updated at: 2021-02-20 14:07:58 UTC  
> Url: https://github.com/boostorg/math/issues/535#issuecomment-782659741  

Both of those tests call `std::normal_distribution` which is supposed to only have defined behavior for float, double and long double. On architectures like s390x that substitute __float128 for long double does this become a mixed precision/UB error? If you try to add a __float128 test on x86_64 you get the following error:  
  
```  
/usr/include/c++/10/bits/random.tcc: In instantiation of ‘std::normal_distribution<_RealType>::result_type std::normal_distribution<_RealType>::operator()(_UniformRandomNumberGenerator&, const std::normal_distribution<_RealType>::param_type&) [with _UniformRandomNumberGenerator = std::mersenne_twister_engine<long unsigned int, 64, 312, 156, 31, 13043109905998158313, 29, 6148914691236517205, 17, 8202884508482404352, 37, 18444473444759240704, 43, 6364136223846793005>; _RealType = __float128; std::normal_distribution<_RealType>::result_type = __float128]’:  
/usr/include/c++/10/bits/random.h:2080:27:   required from ‘std::normal_distribution<_RealType>::result_type std::normal_distribution<_RealType>::operator()(_UniformRandomNumberGenerator&) [with _UniformRandomNumberGenerator = std::mersenne_twister_engine<long unsigned int, 64, 312, 156, 31, 13043109905998158313, 29, 6148914691236517205, 17, 8202884508482404352, 37, 18444473444759240704, 43, 6364136223846793005>; _RealType = __float128; std::normal_distribution<_RealType>::result_type = __float128]’  
random.cpp:11:29:   required from here  
/usr/include/c++/10/bits/random.tcc:1793:56: error: call of overloaded ‘log(std::normal_distribution<__float128>::result_type&)’ is ambiguous  
 1793 |      const result_type __mult = std::sqrt(-2 * std::log(__r2) / __r2);  
      |                                                ~~~~~~~~^~~~~~  
In file included from /usr/include/features.h:465,  
                 from /usr/include/c++/10/x86_64-redhat-linux/bits/os_defines.h:39,  
                 from /usr/include/c++/10/x86_64-redhat-linux/bits/c++config.h:2621,  
                 from /usr/include/c++/10/cmath:41,  
                 from /usr/include/c++/10/random:38,  
                 from random.cpp:1:  
/usr/include/bits/mathcalls.h:104:1: note: candidate: ‘double log(double)’  
  104 | __MATHCALL_VEC (log,, (_Mdouble_ __x));  
      | ^~~~~~~~~~~~~~  
In file included from /usr/include/c++/10/random:38,  
                 from random.cpp:1:  
/usr/include/c++/10/cmath:338:3: note: candidate: ‘constexpr float std::log(float)’  
  338 |   log(float __x)  
      |   ^~~  
/usr/include/c++/10/cmath:342:3: note: candidate: ‘constexpr long double std::log(long double)’  
  342 |   log(long double __x)  
      |   ^~~  
```  
  
The GCC farm does not look to have any s390x platforms in [their matrix](https://cfarm.tetaneutral.net/machines/list/).  
  
Edit:  
The impl also only casts some of the numbers to the literals which could [lead to more precision loss](https://www.boost.org/doc/libs/1_75_0/libs/math/doc/html/math_toolkit/float128_hints.html)  
```  
		__x = result_type(2.0) * __aurng() - 1.0;  
		__y = result_type(2.0) * __aurng() - 1.0;  
```

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-02-21 11:26:09 UTC  
> Url: https://github.com/boostorg/math/issues/535#issuecomment-782841730  

I don't think so: `__float128` *is a* `long double` so it should be supported and well defined.  What well may happen though, is for any given precision, we get slightly different results from the RNG.
