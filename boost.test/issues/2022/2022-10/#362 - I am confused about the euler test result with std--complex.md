# #362 - I am confused about the euler test result with std::complex [Closed]

> Username: fasxmut  
> Created at: 2022-10-18 13:20:40 UTC  
> Updated at: 2022-10-24 03:38:09 UTC  
> Closed at: 2022-10-24 03:38:09 UTC  
> Url: https://github.com/boostorg/test/issues/362  

c++ code:  
```cpp  
#define BOOST_TEST_MODULE EULER_TEST_MODULE_01  
  
#include <boost/test/unit_test.hpp>  
#include <complex>  
#include <iostream>  
  
namespace ut = boost::unit_test;  
  
using namespace std::complex_literals;  
  
using namespace std::numbers;  
  
constexpr auto im = 1i;  
constexpr auto zero = 0i;  
  
BOOST_AUTO_TEST_CASE(TEST_CASE_01) {  
        auto result = std::exp(im*pi) + 1.0;  
        std::cout << "result=> " << result << '\n';  
        std::cout << "real=> " << result.real() << '\n';  
        std::cout << "imag=> " << result.imag() << '\n';  
        BOOST_CHECK_CLOSE(result.real(), 0.0, 0.01);  
        BOOST_CHECK_CLOSE(result.imag(), 0.0, 0.01);  
        BOOST_CHECK_CLOSE(2.5, 3.5, 1.0);  
        BOOST_CHECK_CLOSE(3.5, 2.5, 1.0);  
}  
```  
  
Test Result:  
```  
Running 1 test case...  
result=> (0,1.22465e-16)  
real=> 0  
imag=> 1.22465e-16  
euler.cpp(22): error: in "TEST_CASE_01": difference{1.79769e+308} between result.imag(){1.2246467991473532e-16} and 0.0{0} exceeds 0.01%  
euler.cpp(23): error: in "TEST_CASE_01": difference{0.4} between 2.5{2.5} and 3.5{3.5} exceeds 1%  
euler.cpp(24): error: in "TEST_CASE_01": difference{0.4} between 3.5{3.5} and 2.5{2.5} exceeds 1%  
  
*** 3 failures are detected in the test module "EULER_TEST_MODULE_01"  
```  
  
The difference of the euler test close is 1.79769e+308, why and how?

---

## Comment 1

> Username: fasxmut  
> Created at: 2022-10-24 03:37:52 UTC  
> Url: https://github.com/boostorg/test/issues/362#issuecomment-1288370207  

Sorry. I got it.   
  
use BOOST_CHECK_SMALL  
  
```cpp  
BOOST_CHECK_SMALL(result.imag(), 1e-10);  
```
