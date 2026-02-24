# #364 - boost::math::float_distance broken in variable precision mpfr case [Closed]

> Username: NAThompson  
> Created at: 2020-06-05 18:58:35 UTC  
> Updated at: 2022-10-17 23:56:30 UTC  
> Closed at: 2022-10-17 23:56:29 UTC  
> Url: https://github.com/boostorg/math/issues/364  

To reproduce:  
  
```cpp  
#include <boost/multiprecision/mpfr.hpp>  
#include <boost/math/special_functions/next.hpp>  
  
int main()  
{  
    using namespace boost::multiprecision;  
    mpfr_float::default_precision(100);  
    mpfr_float G1 = boost::math::constants::pi<mpfr_float>();  
    mpfr_float::default_precision(200);  
    mpfr_float G2 = boost::math::constants::pi<mpfr_float>();  
    mpfr_float::default_precision(100);  
    mpfr_float G3 = G2;  
    auto dist = boost::math::float_distance(G1, G3);  
    std::cout << dist << "\n";  
}  
```  
  
Output with g++-9 and `--std=c++11`:  
  
```  
$ ./main.x  
Assertion failed: (result == floor(result)), function float_distance_imp, file ../../boost/math/special_functions/next.hpp, line 546.  
[1]    27275 abort      ./main.x  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-06-06 08:05:46 UTC  
> Url: https://github.com/boostorg/math/issues/364#issuecomment-640009571  

Ugh... I don't know where/how to fix this at present, as in mixed float/double arguments this makes little sense, but catching the case is tricky.

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-06-06 13:07:48 UTC  
> Updated at: 2020-06-06 13:08:58 UTC  
> Url: https://github.com/boostorg/math/issues/364#issuecomment-640058653  

BTW, if you don't put the assert in, then the result is kinda sensible: 0.24ULPs.   
  
Why doesn't this make sense? I reset the precision to the original value. . .

---

## Comment 3

> Username: NAThompson  
> Created at: 2022-10-17 23:56:29 UTC  
> Url: https://github.com/boostorg/math/issues/364#issuecomment-1281642475  

I now believe this to not make sense! Just took me a few years to realize it!
