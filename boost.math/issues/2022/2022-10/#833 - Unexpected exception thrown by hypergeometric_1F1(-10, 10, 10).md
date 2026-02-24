# #833 - Unexpected exception thrown by hypergeometric_1F1(-10, 10, 10) [Closed]

> Username: WarrenWeckesser  
> Created at: 2022-10-05 14:42:50 UTC  
> Updated at: 2022-10-07 08:35:28 UTC  
> Closed at: 2022-10-07 08:35:28 UTC  
> Url: https://github.com/boostorg/math/issues/833  

When I run this code:  
```  
//  
// hyp1f1_check.cpp  
//  
  
#include <iostream>  
#include <iomanip>  
#include <boost/math/special_functions/hypergeometric_1F1.hpp>  
  
using namespace boost::math;  
  
  
int main(int argc, char *argv[])  
{  
    double b = 10.0;  
    double y = hypergeometric_1F1(-10.0, b, 10.0);  
    std::cout << std::scientific << std::setw(16)  
        << std::setprecision(12) << y << std::endl;  
  
    return 0;  
}  
```  
an exception is thrown:  
```  
$ ./hyp1f1_check  
terminate called after throwing an instance of 'boost::wrapexcept<boost::math::rounding_error>'  
  what():  Error in function boost::math::trunc<long double>(long double): Value -inf can not be represented in the target integer type.  
Aborted (core dumped)  
```  
If `b` is changed to 9.99 or 10.01, the code runs successfully.

---

## Comment 1

> Username: WarrenWeckesser  
> Created at: 2022-10-05 14:56:23 UTC  
> Updated at: 2022-10-05 15:58:24 UTC  
> Url: https://github.com/boostorg/math/issues/833#issuecomment-1268557619  

The same problem appears to occur with most calls of the form `hypergeometric_1F1(-n, x, x)`, where `n` is a positive integer and `x` is positive.  
  
It looks like `hypergeometric_1F1_backward_recurrence_for_negative_a(...)` is called, and with `a` an integer and `b == z`, the variable `second` ends up being 0.  Then when `apply_recurrence_relation_backward(...)` is  called, it calls `itrunc(log(fabs(second)))`, and that triggers the exception.
