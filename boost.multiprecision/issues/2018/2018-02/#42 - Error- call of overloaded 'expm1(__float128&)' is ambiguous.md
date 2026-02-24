# #42 - Error: call of overloaded 'expm1(__float128&)' is ambiguous [Closed]

> Username: NAThompson  
> Created at: 2018-02-02 23:23:32 UTC  
> Updated at: 2018-02-04 08:44:00 UTC  
> Closed at: 2018-02-04 08:44:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/42  

Code:  
  
```  
#include <quadmath.h>  
#include <boost/math/special_functions/math_fwd.hpp>  
  
int main(int argc, char** argv)  
{  
    __float128 x = 0.012Q;  
    auto y = expm1(x);  
    return 0;  
}  
```  
  
Compilation:  
  
```  
g++-7 --std=c++1z -O3 -march=native -Wfatal-errors -fext-numeric-literals -I./include main.cpp -o main.x -pthread -lquadmath  
```  
  
Error message:  
  
```  
main.cpp: In function 'int main(int, char**)':  
main.cpp:7:21: error: call of overloaded 'expm1(__float128&)' is ambiguous  
     auto y = expm1(x);  
                     ^  
compilation terminated due to -Wfatal-errors.  
make: *** [main.x] Error 1  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-02-04 08:44:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/42#issuecomment-362890713  

Not our issue: there is no expm1 overload for type __float128, so the compiler doesn't know whether to call the float/double or long double overloads.  
The include of math_fwd.hpp is completely superfluous in this context, as while it defines boost::math::expm1, that is not in scope in your test case.
