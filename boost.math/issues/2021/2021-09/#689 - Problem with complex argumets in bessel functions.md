# #689 - Problem with complex argumets in bessel functions [Closed]

> Username: dgizer  
> Created at: 2021-09-08 00:17:23 UTC  
> Updated at: 2021-09-08 13:43:47 UTC  
> Closed at: 2021-09-08 13:43:46 UTC  
> Url: https://github.com/boostorg/math/issues/689  

When I use boost::math::cyl_bessel_i(0, z1) where z1 is of complex<double> type I've got the error like that:   
  
```  
 boost/math/special_functions/bessel.hpp:177:9: error: no match for ‘operator<’ (operand types are ‘std::complex<double>’ and ‘int’)  
    if(x < 0)  
```  
  
the code under compilation is:  
  
```  
#include <iostream>  
#include <complex>  
#include <boost/math/special_functions/bessel.hpp>  
  
int main(int argc, char *argv[])  
{  
    std::complex<double> z1 = {0, 1.0};  
    std::complex<double> res = boost::math::cyl_bessel_i(0, z1);  
    std::cout << "res = " << res << std::endl;  
  
    return 0;  
}  
```  
  
The compilation command is:  
`g++ -I/home/artur/local/boost/include test_complex.cpp -o run_complex`  
  
The partial compilation results are (please let me know if you need full output):  
```  
In file included from test_complex.cpp:3:  
/home/artur/local/boost/include/boost/math/special_functions/bessel.hpp: In instantiation of ‘T boost::math::detail::cyl_bessel_i_imp(T, T, const Policy&) [with T = std::complex<double>; Policy = boost::math::policies::policy<boost::math::policies::promote_float<false>, boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>]’:  
/home/artur/local/boost/include/boost/math/special_functions/bessel.hpp:558:101:   required from ‘typename boost::math::detail::bessel_traits<T1, T2, Policy>::result_type boost::math::cyl_bessel_i(T1, T2, const Policy&) [with T1 = int; T2 = std::complex<double>; Policy = boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>; typename boost::math::detail::bessel_traits<T1, T2, Policy>::result_type = std::complex<double>]’  
/home/artur/local/boost/include/boost/math/special_functions/bessel.hpp:564:23:   required from ‘typename boost::math::detail::bessel_traits<T1, T2, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >::result_type boost::math::cyl_bessel_i(T1, T2) [with T1 = int; T2 = std::complex<double>; typename boost::math::detail::bessel_traits<T1, T2, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >::result_type = std::complex<double>]’  
test_complex.cpp:69:63:   required from here  
/home/artur/local/boost/include/boost/math/special_functions/bessel.hpp:177:9: error: no match for ‘operator<’ (operand types are ‘std::complex<double>’ and ‘int’)  
    if(x < 0)  
       ~~^~~  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/artur/local/boost/include/boost/math/special_functions/bessel.hpp:180:15: error: no matching function for call to ‘floor(std::complex<double>&)’  
       if(floor(v) == v)  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/include/c++/8/cmath:272:5: error: no type named ‘__type’ in ‘struct __gnu_cxx::__enable_if<false, double>’  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/artur/local/boost/include/boost/math/special_functions/bessel.hpp:192:9: error: no match for ‘operator==’ (operand types are ‘std::complex<double>’ and ‘int’)  
    if(x == 0)  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/artur/local/boost/include/boost/math/special_functions/bessel.hpp:194:17: error: no match for ‘operator==’ (operand types are ‘std::complex<double>’ and ‘int’)  
       return (v == 0) ? static_cast<T>(1) : static_cast<T>(0);  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/artur/local/boost/include/boost/math/special_functions/bessel.hpp:196:9: error: no match for ‘operator==’ (operand types are ‘std::complex<double>’ and ‘float’)  
    if(v == 0.5f)  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/artur/local/boost/include/boost/math/special_functions/bessel.hpp:199:12: error: no match for ‘operator>=’ (operand types are ‘std::complex<double>’ and ‘std::complex<double>’)  
       if(x >= tools::log_max_value<T>())  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/artur/local/boost/include/boost/math/special_functions/bessel.hpp:201:22: error: no match for ‘operator/’ (operand types are ‘std::complex<double>’ and ‘int’)  
          T e = exp(x / 2);  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/artur/local/boost/include/boost/math/special_functions/bessel.hpp:202:33: error: no match for ‘operator*’ (operand types are ‘int’ and ‘std::complex<double>’)  
          return e * (e / sqrt(2 * x * constants::pi<T>()));  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/artur/local/boost/include/boost/math/special_functions/bessel.hpp:204:21: error: no match for ‘operator/’ (operand types are ‘int’ and ‘std::complex<double>’)  
       return sqrt(2 / (x * constants::pi<T>())) * sinh(x);  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/artur/local/boost/include/boost/math/tools/precision.hpp:41:19: error: static assertion failed: Type T must be specialized  
    static_assert( ::std::numeric_limits<T>::is_specialized, "Type T must be specialized");  
                   ^~  
/home/artur/local/boost/include/boost/math/tools/precision.hpp:42:56: error: static assertion failed: Type T must have a radix of 2 or 10  
    static_assert( ::std::numeric_limits<T>::radix == 2 || ::std::numeric_limits<T>::radix == 10, "Type T must have a radix of 2 or 10");  
                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/artur/local/boost/include/boost/math/special_functions/bessel.hpp:208:12: error: no match for ‘operator==’ (operand types are ‘std::complex<double>’ and ‘int’)  
       if(v == 0)  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/artur/local/boost/include/boost/math/special_functions/bessel.hpp:212:12: error: no match for ‘operator==’ (operand types are ‘std::complex<double>’ and ‘int’)  
       if(v == 1)  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/artur/local/boost/include/boost/math/special_functions/bessel.hpp:217:10: error: no match for ‘operator>’ (operand types are ‘std::complex<double>’ and ‘int’)  
    if((v > 0) && (x / v < 0.25))  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/artur/local/boost/include/boost/math/special_functions/bessel.hpp:217:25: error: no match for ‘operator<’ (operand types are ‘std::complex<double>’ and ‘double’)  
    if((v > 0) && (x / v < 0.25))  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/artur/local/boost/include/boost/math/tools/precision.hpp:52:19: error: static assertion failed: Type T must be specialized  
    static_assert( ::std::numeric_limits<T>::is_specialized, "Type T must be specialized");  
  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2021-09-08 08:16:25 UTC  
> Url: https://github.com/boostorg/math/issues/689#issuecomment-915021677  

> use `boost::math::cyl_bessel_i(0, z1)` where `z1` is of complex type  
  
I believe the special function arguments are limited to real only. I think @jzmaddock can confirm?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-09-08 13:43:46 UTC  
> Url: https://github.com/boostorg/math/issues/689#issuecomment-915251722  

Correct.  Bessel functions have not been implemented for complex arguments - this is principally an issue with testing - the domain of the functions with complex args is so large that they become in a sense "untestable".
