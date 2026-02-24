# #328 - Lambert-W test tolerances are too stringent or test values lack precision. [Closed]

> Username: NAThompson  
> Created at: 2020-03-27 11:42:39 UTC  
> Updated at: 2020-03-31 00:27:13 UTC  
> Closed at: 2020-03-31 00:27:13 UTC  
> Url: https://github.com/boostorg/math/issues/328  

Compiled with `g++-9`, we have:  
  
```  
$ ./test_lambert_w.x  
Running 2 test cases...  
GCC 9.2.0  
  
Testing type f  
Tolerance 2 * epsilon == 2.38419e-07  
Precision 6 decimal digits, max_digits10 = 9  
-exp(-1) = -0.367879450  
  
Testing type d  
Tolerance 2 * epsilon == 4.44089e-16  
Precision 15 decimal digits, max_digits10 = 17  
-exp(-1) = -0.36787944117144233  
  
Testing type e  
Tolerance 16 * epsilon == 1.73472e-18  
Precision 18 decimal digits, max_digits10 = 21  
-exp(-1) = -0.367879441171442321583  
  
Testing type N5boost4math8concepts12real_conceptE  
Tolerance 2 * epsilon == 2.1684e-19  
Precision 0 decimal digits, max_digits10 = 0  
-exp(-1) = -0.4  
  
BOOST_MATH_TEST_FLOAT128 NOT defined so NO float128 tests.  
Tolerance 1 * epsilon == 2.22045e-16  
test_lambert_w.cpp:1035: error: in "test_range_of_double_values": difference{2.61848e-09} between lambert_w0(create_test_value<RealType>( -0.36787944117144228Q, "-0.36787944117144228", boost::integral_constant<bool, std::numeric_limits<RealType>::is_specialized && (std::numeric_limits<RealType>::radix == 2) && (std::numeric_limits<RealType>::digits <= 113) && boost::is_convertible<largest_float, RealType>::value>(), boost::integral_constant<bool, boost::is_constructible<RealType, const char*>::value>())){-0.99999998154505632} and create_test_value<RealType>( -0.99999997892657588Q, "-0.99999997892657588", boost::integral_constant<bool, std::numeric_limits<RealType>::is_specialized && (std::numeric_limits<RealType>::radix == 2) && (std::numeric_limits<RealType>::digits <= 113) && boost::is_convertible<largest_float, RealType>::value>(), boost::integral_constant<bool, boost::is_constructible<RealType, const char*>::value>()){-0.99999997892657588} exceeds 2.2204460492503131e-16  
test_lambert_w.cpp:1040: error: in "test_range_of_double_values": difference{4.64453e-10} between lambert_w0(create_test_value<RealType>( -0.36787944117144222Q, "-0.36787944117144222", boost::integral_constant<bool, std::numeric_limits<RealType>::is_specialized && (std::numeric_limits<RealType>::radix == 2) && (std::numeric_limits<RealType>::digits <= 113) && boost::is_convertible<largest_float, RealType>::value>(), boost::integral_constant<bool, boost::is_constructible<RealType, const char*>::value>())){-0.9999999746548851} and create_test_value<RealType>( -0.99999997419043196Q, "-0.99999997419043196", boost::integral_constant<bool, std::numeric_limits<RealType>::is_specialized && (std::numeric_limits<RealType>::radix == 2) && (std::numeric_limits<RealType>::digits <= 113) && boost::is_convertible<largest_float, RealType>::value>(), boost::integral_constant<bool, boost::is_constructible<RealType, const char*>::value>()){-0.99999997419043196} exceeds 2.2204460492503131e-16  
(std::numeric_limits<RealType>::min)() 2.2250738585072014e-308  
  
*** 2 failures are detected in the test module "Master Test Suite"  
```  
  
The issue is that the condition number of function evaluation near these test points is tending to infinity, so any change in rounding in any part of the computation is causing large changes in the result.
