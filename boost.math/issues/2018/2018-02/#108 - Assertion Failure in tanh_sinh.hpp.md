# #108 - Assertion Failure in tanh_sinh.hpp [Closed]

> Username: NAThompson  
> Created at: 2018-02-06 20:23:12 UTC  
> Updated at: 2018-04-25 02:13:32 UTC  
> Closed at: 2018-04-25 02:13:32 UTC  
> Url: https://github.com/boostorg/math/issues/108  

Code to reproduce:  
  
```  
#include <boost/math/constants/constants.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/math/quadrature/tanh_sinh.hpp>  
  
using boost::multiprecision::cpp_bin_float_quad;  
  
  
template<class Real>  
void test_omega()  
{  
    using std::sin;  
    using std::cos;  
    using boost::math::quadrature::tanh_sinh;  
    using boost::math::constants::two_pi;  
    auto f = [](Real x)->Real {  
        return (1+2*cos(x)+x*sin(x))/(1+2*x*sin(x)+x*x);  
    };  
  
    tanh_sinh<Real> ts;  
    Real t = ts.integrate(f, (Real) 0, 100*two_pi<Real>());  
}  
  
  
int main()  
{  
    test_omega<cpp_bin_float_quad>();  
    return 0;  
}  
```  
  
Failure message:  
  
```  
clang++ --std=c++1z -Wfatal-errors -I./include reproduce.cpp -o reproduce.x -pthread -lbenchmark  
Assertion failed: (position != b), function operator(), file ./include/boost/math/quadrature/tanh_sinh.hpp, line 170.  
[1]    76470 abort      ./reproduce.x  
```  
  
Commenting out line 170 of this file fixes the problem, but perhaps only because the integrand isn't singular at the endpoints.

---

## Comment 1

> Username: NAThompson  
> Created at: 2018-02-18 21:53:32 UTC  
> Url: https://github.com/boostorg/math/issues/108#issuecomment-366551212  

Astonishingly, I believe this is because of a (correctly implemented) roundoff tie in multiprecision's quad. I see that the bad quadrature node `z` is two representables less than 1, so that `z = 1-eps`, where `eps` is the quad machine epsilon. The code then calculates `avg + diff*z = 100*Pi + 100*Pi(1-eps) = 200*Pi(1 - eps/2)`. Now the round must be to either `pf(200*Pi)` where `pf` denotes "previous float", or to `200*Pi`, but the rules say to round up, so `200*Pi` it is. Since the boundary could be singular, this is a problem, so the assert kills the program.  
  
I have a ham-handed fix: Namely replace the assert in line 170 with  
  
```  
if (position == b)  
{  
    using std::numeric_limits;  
    position = nextafter(position, -numeric_limits<Real>::lowest());  
}  
```  
  
printf debug code to validate these claims: Place the following in `tanh_sinh.hpp` line 170:  
  
```  
if (position == b)  
{  
    std::cout << std::setprecision(2*std::numeric_limits<Real>::digits10);  
    std::cout << "position    = " << position << std::endl;  
    std::cout << "Limits = [" << a << ", " << b << "]" << std::endl;  
    std::cout << "                z = " << z << std::endl;  
    std::cout << "previous float(1) = " << nextafter((Real) 1, 0) << std::endl;  
    std::cout << "previous float(1) = " << nextafter(nextafter((Real) 1, 0), 0) << std::endl;  
    std::cout << "1 - eps           = " << 1 - numeric_limits<Real>::epsilon() << std::endl;  
    std::cout << "1 - eps/2         = " << 1 - numeric_limits<Real>::epsilon()/2 << std::endl;  
    std::cout << "        avg  = " << avg  << std::endl;  
    std::cout << "        diff = " << diff << std::endl;  
    std::cout << "     pf(diff)= " << nextafter(diff, 0) << std::endl;  
    std::cout << "      diff*z = " << diff*z << std::endl;  
    std::cout << "avg + diff*z = " << avg + diff*z << std::endl;  
    BOOST_ASSERT(position != b);  
}  
```  
  
Result:  
  
```  
position    = 628.318530717958647692528676655900584147716907784078033717418531462  
Limits = [0, 628.318530717958647692528676655900584147716907784078033717418531462]  
                z = 0.999999999999999999999999999999999807407005561276414694402205741507  
            pf(1) = 0.999999999999999999999999999999999903703502780638207347201102870754  
        pf(pf(1)) = 0.999999999999999999999999999999999807407005561276414694402205741507  
1 - eps           = 0.999999999999999999999999999999999807407005561276414694402205741507  
1 - eps/2         = 0.999999999999999999999999999999999903703502780638207347201102870754  
        avg  = 314.159265358979323846264338327950292073858453892039016858709265731  
        diff = 314.159265358979323846264338327950292073858453892039016858709265731  
     pf(diff)= 314.159265358979323846264338327950242770051877578801178625673935557  
      diff*z = 314.159265358979323846264338327950242770051877578801178625673935557  
avg + diff*z = 628.318530717958647692528676655900584147716907784078033717418531462  
```

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-04-22 18:48:22 UTC  
> Url: https://github.com/boostorg/math/issues/108#issuecomment-383403435  

Fixed in: https://github.com/boostorg/math/commit/b4ec109b830cc294dcbceb9a943e544aa5560543 by changing to a better calculation method when we're close to an endpoint.  
  
There are some open issues as in the commit message:  
  
Open questions:   
1) Can this still fail if the endpoint is a very large value?  For example if we have endpoints of [+large, +larger] where the difference between them is small?  
2) Do we still need the asserts, or should they be replaced by some other check?  
3) If the endpoints are replaced by some other check we could either massage the position to be one representable value below the endpoint, or else simply return 0.  The former risks evaluating at the same point over and over as we go down levels, the latter would seem to be incorrect for functions which do not go to zero at the endpoint.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-04-23 07:08:50 UTC  
> Url: https://github.com/boostorg/math/issues/108#issuecomment-383475812  

>Can this still fail if the endpoint is a very large value? For example if we have endpoints of [+large, +larger] where the difference between them is small?  
  
Never mind, this *should* have been taken care of by left_min_value and right_min_value protecting against this kind of error... I might need to figure out a way of verifying that they're calculated correctly.

---

## Comment 4

> Username: NAThompson  
> Created at: 2018-04-25 02:13:32 UTC  
> Url: https://github.com/boostorg/math/issues/108#issuecomment-384139336  

Validated the fix works.
