# #247 - Significant slowdown in binomial coefficient between boost 1.55 and 1.70 [Closed]

> Username: BluescreenProductions  
> Created at: 2019-08-26 15:04:29 UTC  
> Updated at: 2019-10-20 10:40:18 UTC  
> Closed at: 2019-10-20 10:40:18 UTC  
> Url: https://github.com/boostorg/math/issues/247  

I recently upgraded my boost version from 1.55 to 1.70 and noted a significant increase in runtime. My program relies on calculating (a lot) of binomial coefficients. After profiling, I noted that the program using version 1.55 uses the lanczos approximation to calculate the binomial coefficients, whereas the program using version 1.70 did not (same code, only different boost versions). Here is a minimal reproducible example to show the different behavior of version 1.55 compared to 1.70. I compiled on Ubuntu 18 using gcc (version 7.4.0) with the command g++ -std=c++14 -g -O2 -o test test.cpp.  
  
test.cpp:  
  
```c++  
#include <iostream>  
#include <limits>  
#include <boost/math/special_functions/binomial.hpp>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
#include <boost/multiprecision/cpp_int.hpp>  
  
int main(){  
        namespace bmp = boost::multiprecision;  
        unsigned int l = 234, m=23;  
  
        typedef typename boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u, int, void>, (boost::multiprecision::expression_template_option)1> t;  
        std::cout << std::numeric_limits<t>::digits10 << "\n";  
        std::cout << boost::math::binomial_coefficient<t>(l, m) << "\n";  
        return 0;  
}  
```  
  
My question:  
How can I speed up my program again? (And for my personal interest, is there a reason for not choosing the approximation here?)  
  
Further things that might be helpful:  
If I understand the code correctly, the type of approximation is chosen based on the template parameter of boost::math::binomial_coefficient (from math/bindings/e_float.hpp):  
  
```c++  
struct lanczos<boost::math::ef::e_float, Policy>  
{  
   typedef typename mpl::if_c<  
      std::numeric_limits< ::e_float>::digits10 < 22,  
      lanczos13UDT,  
      typename mpl::if_c<  
         std::numeric_limits< ::e_float>::digits10 < 36,  
         lanczos22UDT,  
         typename mpl::if_c<  
            std::numeric_limits< ::e_float>::digits10 < 50,  
            lanczos31UDT,  
            typename mpl::if_c<  
               std::numeric_limits< ::e_float>::digits10 < 110,  
               lanczos61UDT,  
               undefined_lanczos  
            >::type  
         >::type  
      >::type  
   >::type type;  
};  
```  
  
If I lower the number of digits in test.cpp say from `cpp_dec_float<50u, int, void>` to `cpp_dec_float<20u, int, void>`, both versions, 1.55 and 1.70, use the approximation "lanczos22UDT". If I use the version of test.cpp from above, version 1.55 uses lanczos31UDT but version 1.70 uses undefined_lanczos, i.e. no approximation. e_float.hpp is identical in version 1.55 and 1.70. I further noted that boost version 1.65 also uses no approximation for test.cpp.  
  
I use valgrind --tool=callgrind ./test and kcachegrind for profiling.  
  
Thank you for looking into this!

---

## Comment 1

> Username: BluescreenProductions  
> Created at: 2019-09-03 13:40:34 UTC  
> Url: https://github.com/boostorg/math/issues/247#issuecomment-527463145  

In the documentation of boost::math::beta(), there is a line stating that I can generate a Lanczos approximation for my type using the program `libs/math/tools/lanczos_generator.cpp`!  
However, I was not able to find any documentation about how to use the program. Any hints on how to use it?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-09-03 16:08:32 UTC  
> Url: https://github.com/boostorg/math/issues/247#issuecomment-527527640  

Thanks for raising this - and apologies for the delay in getting back to you.  
  
First the explanation: the lanczos approximations only work really well (accurately) when they're optimised for the specific precision you're using.  In the past since we basically had no other choice, we used generic lanczos approximations for multiprecision types which while working broadly OK, were subject to significant roundoff error during evaluation.  We now have a Sterling approximation which we didn't have before (the addition of Bernoulli number support made it possible), and it's a true arbitrary precision approximation with much less roundoff error than un-optimised lanczos approximations.  I need to investigate the performance issues, but it I suspect that generation of Bernoulli numbers may be at fault.  
  
The easiest way to get things back to where they were is to include boost/math/bindings/detail/big_lanczos.hpp and then specialize the lanczos traits class for your number type, picking the approximation closest to the precision of your number type.  
  
As for lanczos_generator.cpp - it's quite old, and I suspect needs some work - let me get back to you on that!

---

## Comment 3

> Username: BluescreenProductions  
> Created at: 2019-09-04 12:25:53 UTC  
> Url: https://github.com/boostorg/math/issues/247#issuecomment-527877642  

Thank you much, it worked! I specialized the lanczos struct for my type in roughly the same manner as in `boost/math/bindings/rr.hpp`. It is now again similarly fast as with 1.55. Still strange that 1.55 and 1.70 choose different lanczos approximations for my type.  
  
I didn't know about the Sterling approximation before. Is there a way of using this approximation for the binomial coefficients in 1.71?
