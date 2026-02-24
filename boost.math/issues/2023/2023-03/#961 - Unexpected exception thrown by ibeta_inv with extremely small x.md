# #961 - Unexpected exception thrown by ibeta_inv with extremely small x. [Closed]

> Username: WarrenWeckesser  
> Created at: 2023-03-03 22:59:06 UTC  
> Updated at: 2023-03-05 13:18:28 UTC  
> Closed at: 2023-03-05 13:18:28 UTC  
> Url: https://github.com/boostorg/math/issues/961  

I'm using the develop branch, commit 82ccb8590723d9a954384e6b453b647129c0799d.  The compiler:  
```  
$ g++ --version  
g++ (Ubuntu 11.3.0-1ubuntu1~22.04) 11.3.0  
```  
  
The code below throws the exception  
  
```  
ibeta_inv_tiny_x: /home/warren/repos/git/forks/boost/math/include/boost/math/special_functions/  
detail/ibeta_inverse.hpp:122: T boost::math::detail::temme_method_1_ibeta_inverse(T, T, T, const Policy&)  
[with T = long double; Policy =   
boost::math::policies::policy<boost::math::policies::promote_float<false>,   
boost::math::policies::promote_double<false>, boost::math::policies::default_policy,   
boost::math::policies::default_policy, boost::math::policies::default_policy,   
boost::math::policies::default_policy, boost::math::policies::default_policy,   
boost::math::policies::default_policy, boost::math::policies::default_policy,   
boost::math::policies::default_policy, boost::math::policies::default_policy>]:   
Assertion `x >= 0' failed.  
Aborted (core dumped)  
```  
At line 122 of `ibeta_inverse.hpp`, the value of `x` that triggers the exception is -5.42101e-20.  
  
  
  
There are comments in the code below that give nearby values of the input `x` that exhibit different behaviors.  
  
`ibeta_inv_tiny_x.cpp`:  
```  
#include <iostream>  
#include <iomanip>  
#include <cfenv>  
#include <boost/math/special_functions/beta.hpp>  
  
using namespace std;  
using namespace boost::math;  
  
void show_fp_exception_flags()  
{  
    if (std::fetestexcept(FE_DIVBYZERO)) {  
        cout << " FE_DIVBYZERO";  
    }  
    // FE_INEXACT is common and not interesting.  
    // if (std::fetestexcept(FE_INEXACT)) {  
    //     cout << " FE_INEXACT";  
    // }  
    if (std::fetestexcept(FE_INVALID)) {  
        cout << " FE_INVALID";  
    }  
    if (std::fetestexcept(FE_OVERFLOW)) {  
        cout << " FE_OVERFLOW";  
    }  
    if (std::fetestexcept(FE_UNDERFLOW)) {  
        cout << " FE_UNDERFLOW";  
    }  
    cout << endl;  
}  
  
int main(int argc, char *argv[])  
{  
    double a = 14.208308325339239;  
    double b = a;  
  
    double p = 7.703145458496392e-307;  // Throws exception: Assertion `x >= 0' failed.  
    // double p = 7.8e-307;  // No flags set, returns 8.57354094063444939e-23  
    // double p = 7.7e-307;  // FE_UNDERFLOW set, returns 0.0  
  
    std::feclearexcept(FE_ALL_EXCEPT);  
  
    double x = ibeta_inv(a, b, p);  
  
    show_fp_exception_flags();  
  
    std::cout << std::scientific << std::setw(24)  
              << std::setprecision(17) << x << std::endl;  
  
    return 0;  
}  
  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-03-04 11:51:30 UTC  
> Url: https://github.com/boostorg/math/issues/961#issuecomment-1454712788  

I don't reproduce with MSVC, but I think I see the issue anyway.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2023-03-04 19:27:11 UTC  
> Url: https://github.com/boostorg/math/issues/961#issuecomment-1454849585  

Referenced PR improves things quite a bit.... However, the ibeta is potentially so extreme, that most root finders will fail at some point.  The function basically resembles a "square wave" in extreme cases switching from 0 to 1 in the blink of an eye and arbitrarily close to either end point.  And for every extreme example, you can always create a worse one!

---

## Comment 3

> Username: WarrenWeckesser  
> Created at: 2023-03-04 19:41:36 UTC  
> Updated at: 2023-03-04 20:08:01 UTC  
> Url: https://github.com/boostorg/math/issues/961#issuecomment-1454853424  

Thanks @jzmaddock.  I'll add that I wasn't originally intending to test the function with such a small `p` and those values of `a` and `b`.  That happened by accident because of a bug in a test script that I was running to check the wrapper of the function in SciPy.  If it was just flaky numerical results, I wouldn't have worried, but the exception crashed Python, and that is something we would like to avoid. :smiley:
