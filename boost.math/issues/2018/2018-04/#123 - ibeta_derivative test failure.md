# #123 - ibeta_derivative test failure [Closed]

> Username: NAThompson  
> Created at: 2018-04-24 13:36:47 UTC  
> Updated at: 2018-10-30 18:39:29 UTC  
> Closed at: 2018-10-30 18:39:28 UTC  
> Url: https://github.com/boostorg/math/issues/123  

Compiled with:  
```  
test$ ../../../b2 --toolset=clang --compiler=clang++ cxxflags=-fsanitize=undefined linkflags=-fsanitize=undefined -j4 -a -q -d0  
====== BEGIN OUTPUT ======  
Running 1 test case...  
Tests run with Clang version 9.1.0 (clang-902.0.39.1), libc++ version 5000, Mac OS  
../../../boost/math/special_functions/relative_difference.hpp:86:46: runtime error: 1.18973e+4932 is outside the range of representable values of type 'double'  
Testing Incomplete Beta Function Derivative: Small Values with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
beta (incomplete)<double> Max = 0.7161 RMS Mean=0.03203  
    worst case at row: 320  
    { 6.3578672409057617, 7.7779402732849121, 0.96864354610443115, 6.6587600748516125e-07 }  
  
beta (incomplete, internal call test)<double> Max = 13.4 RMS Mean=2.624  
    worst case at row: 492  
    { 9.7974834442138672, 3.5463814735412598, 0.438926100730896, 0.23326106479092035 }  
  
Testing Incomplete Beta Function Derivative: Medium Values with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
beta (incomplete)<double> Max = 60.75 RMS Mean=2.717  
    worst case at row: 3  
    { 1.1902070045471191, 72.695472717285156, 0.19963125884532928, 1.5321906748924116e-05 }  
  
beta (incomplete, internal call test)<double> Max = 105.6 RMS Mean=20.4  
    worst case at row: 469  
    { 93.401069641113281, 96.496635437011719, 0.18903611600399017, 1.1333723070206939e-18 }  
  
Testing Incomplete Beta Function Derivative: Large and Diverse Values with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
beta (incomplete)<double> Max = 1.456 RMS Mean=0.0847  
    worst case at row: 225  
    { 0.0012384754372760653, 4809.8876953125, 0.12707412242889404, 1.4433873458058292e-286 }  
  
beta (incomplete, internal call test)<double> Max = 3227 RMS Mean=190.6  
    worst case at row: 1093  
    { 48850.35546875, 289555.46875, 0.13556346297264099, 1.7280447883075433e-45 }  
Peak error greater than expected value of 3000  
./handle_test_result.hpp:172: error: in "test_main": check bounds.first >= max_error_found has failed  
  
Testing Incomplete Beta Function Derivative: Small Integer Values with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
beta (incomplete)<double> Max = 11.74 RMS Mean=0.4647  
    worst case at row: 83  
    { 1, 33, 0.30823621153831482, 0.00024955387659880056 }  
  
beta (incomplete, internal call test)<double> Max = 34.93 RMS Mean=8.427  
    worst case at row: 375  
    { 13, 29, 0.81474220752716064, 6.1650314325423143e-11 }  
  
  
*** 1 failure is detected in the test module "Master Test Suite"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
...failed updating 1 target...  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-04-24 17:19:02 UTC  
> Url: https://github.com/boostorg/math/issues/123#issuecomment-384011985  

The error rate is just very slightly larger than the tolerance - this is normally a "shrug" and increase the tolerance for that specific compiler/platform.

---

## Comment 2

> Username: NAThompson  
> Created at: 2018-10-30 18:39:28 UTC  
> Url: https://github.com/boostorg/math/issues/123#issuecomment-434421247  

Fixed in [c0e93a98](https://github.com/boostorg/math/commit/c0e93a9817784ec6e4888e954ccaafbe9fa60e57).
