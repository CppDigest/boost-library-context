# #705 - powm1 can have exceptions or infinite recursion when x=0 [Closed]

> Username: aherbert  
> Created at: 2021-10-12 11:43:54 UTC  
> Updated at: 2022-01-05 15:41:49 UTC  
> Closed at: 2022-01-05 15:41:49 UTC  
> Url: https://github.com/boostorg/math/issues/705  

The powm1 function has handling for `x > 0` or else. In the else clause it is assumed that `x < 0`. If `x = 0` then the results are invalid:  
```c++  
#include <iostream>  
#include <cmath>  
  
#include <boost/math/special_functions/powm1.hpp>  
  
void result(double x, double y) {  
  double a = pow(x, y) - 1;  
  std::cout << "pow(" << x  
            << ", " << y  
            << ") - 1 = " << a  
            << std::endl;  
  double b = boost::math::powm1(x, y);  
  std::cout << "powm1(" << x  
            << ", " << y  
            << ") = " << b  
            << std::endl;  
}  
  
int main(int argc, const char* argv[]) {  
  // Any case is incorrect  
  if      (argc > 3) result(0, -0.1);  
  else if (argc > 2) result(0,  0.1);  
  else if (argc > 1) result(0, -2);  
  else               result(0,  2);  
}  
```  
Results:  
```  
g++ powm1_bug.cpp -o powm1_bug  
  
./powm1_bug   
pow(0, 2) - 1 = -1  
Segmentation fault (core dumped)  
  
./powm1_bug a  
pow(0, -2) - 1 = inf  
Segmentation fault (core dumped)  
  
./powm1_bug a a  
pow(0, 0.1) - 1 = -1  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<std::domain_error> >'  
  what():  Error in function boost::math::powm1<double>(double, double): For non-integral exponent, expected base > 0 but got 0  
Aborted (core dumped)  
  
./powm1_bug a a a  
pow(0, -0.1) - 1 = inf  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<std::domain_error> >'  
  what():  Error in function boost::math::powm1<double>(double, double): For non-integral exponent, expected base > 0 but got 0  
Aborted (core dumped)  
```  
  
Called with an even integer `y` the function attempts to negate `x` because `x^y == -x^y`. This leads to infinite recursion (because negating zero has no effect) and creates a segmentation fault.  
  
Called with any non integer `y` the function errors because y is not an integer. Raising `0` to any finite power is an allowed result. I would expect:  
```  
powm1(0, 0) = 0  
powm1(0, 0.1) = -1  
powm1(0, -0.1) = inf  
```  
  
This can be fixed by adding a second if statement to the else clause:  
```c++  
   }  
   //else   // Do not assume x is negative  
   else if (x < 0)  
   {  
      // y had better be an integer:  
  
```  
This results in:  
```  
./powm1_bug  
pow(0, 2) - 1 = -1  
powm1(0, 2) = -1  
  
./powm1_bug a  
pow(0, -2) - 1 = inf  
powm1(0, -2) = inf  
  
./powm1_bug a a  
pow(0, 0.1) - 1 = -1  
powm1(0, 0.1) = -1  
  
./powm1_bug a a a  
pow(0, -0.1) - 1 = inf  
powm1(0, -0.1) = inf  
  
```

---

## Comment 1

> Username: mborland  
> Created at: 2022-01-05 15:41:49 UTC  
> Url: https://github.com/boostorg/math/issues/705#issuecomment-1005837519  

Recommended fix has been merged.
