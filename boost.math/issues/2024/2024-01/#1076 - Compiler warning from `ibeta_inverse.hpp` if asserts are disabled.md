# #1076 - Compiler warning from `ibeta_inverse.hpp` if asserts are disabled. [Closed]

> Username: WardBrian  
> Created at: 2024-01-31 16:35:25 UTC  
> Updated at: 2024-02-05 09:26:09 UTC  
> Closed at: 2024-02-05 09:26:08 UTC  
> Url: https://github.com/boostorg/math/issues/1076  

Compiling with `-Wall -DBOOST_DISABLE_ASSERTS` causes an unused variable warning from code added in #1004  
  
  
Example:  
  
```c++  
// file boost.cpp  
#include <boost/math/special_functions/beta.hpp>  
#include <iostream>  
  
int main(int argc, char** argv) {  
  
    double a = argc;  
    std::cout << boost::math::ibeta_inv(a,0.4,0.2);  
  
    return 0;  
}  
```  
  
```  
$ g++ -Wall -DBOOST_DISABLE_ASSERTS -I./boost_1.84.0/ boost.cpp   
In file included from ./boost_1.84.0/boost/math/special_functions/beta.hpp:1721,  
                 from boost.cpp:1:  
./boost_1.84.0/boost/math/special_functions/detail/ibeta_inverse.hpp: In instantiation of ‘boost::math::detail::temme_root_finder<T>::temme_root_finder(T, T) [with T = long double]’:  
./boost_1.84.0/boost/math/special_functions/detail/ibeta_inverse.hpp:304:7:   required from ‘T boost::math::detail::temme_method_2_ibeta_inverse(T, T, T, T, T, const Policy&) [with T = long double; Policy = boost::math::policies::policy<boost::math::policies::promote_float<false>, boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>]’  
./boost_1.84.0/boost/math/special_functions/detail/ibeta_inverse.hpp:615:48:   required from ‘T boost::math::detail::ibeta_inv_imp(T, T, T, T, const Policy&, T*) [with T = long double; Policy = boost::math::policies::policy<boost::math::policies::promote_float<false>, boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>]’  
./boost_1.84.0/boost/math/special_functions/detail/ibeta_inverse.hpp:992:30:   required from ‘boost::math::tools::promote_args_t<T1, T2, T3, T4> boost::math::ibeta_inv(T1, T2, T3, T4*, const Policy&) [with T1 = double; T2 = double; T3 = double; T4 = double; Policy = boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>; boost::math::tools::promote_args_t<T1, T2, T3, T4> = double]’  
./boost_1.84.0/boost/math/special_functions/detail/ibeta_inverse.hpp:1015:20:   required from ‘boost::math::tools::promote_args_t<RT1, RT2, A> boost::math::ibeta_inv(RT1, RT2, RT3) [with RT1 = double; RT2 = double; RT3 = double; boost::math::tools::promote_args_t<RT1, RT2, A> = double]’  
boost.cpp:7:40:   required from here  
./boost_1.84.0/boost/math/special_functions/detail/ibeta_inverse.hpp:29:15: warning: unused variable ‘x_extrema’ [-Wunused-variable]  
   29 |       const T x_extrema = 1 / (1 + a);  
```  
  
  
Versions:  
```  
$ g++ --version  
g++ (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0  
Copyright (C) 2021 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```  
  
Boost 1.84.0

---

## Comment 1

> Username: SteveBronder  
> Created at: 2024-01-31 21:32:08 UTC  
> Url: https://github.com/boostorg/math/issues/1076#issuecomment-1920005077  

I think this can just be fixed by putting an ifdef around the assert code in #1004   
  
```c++  
#ifndef BOOST_DISABLE_ASSERTS  
      const T x_extrema = 1 / (1 + a);  
      BOOST_MATH_ASSERT(0 < x_extrema && x_extrema < 1);  
#endif  
```

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-02-05 09:26:09 UTC  
> Url: https://github.com/boostorg/math/issues/1076#issuecomment-1926549179  

Fixed in develop.
