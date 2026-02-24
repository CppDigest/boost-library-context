# #1145 - Potentially missing inline/constexpr specifier in boost/math/ccmath/fmin.hpp [Closed]

> Username: quxflux  
> Created at: 2024-06-14 06:18:26 UTC  
> Updated at: 2024-06-14 13:59:21 UTC  
> Closed at: 2024-06-14 13:59:21 UTC  
> Url: https://github.com/boostorg/math/issues/1145  

The functions `boost::math::ccmath::fminf` and `boost::math::ccmath::fminl` in [`include/boost/math/ccmath/fmin.hpp`](https://github.com/boostorg/math/blob/master/include/boost/math/ccmath/fmin.hpp) are neither marked `inline` nor `constexpr`.   
This leads to duplicate symbol definitions if `boost/math/ccmath/fmin.hpp` gets included in more than one TU in a binary.   
```  
multiple definition of `boost::math::ccmath::fminf(float, float)';  
multiple definition of `boost::math::ccmath::fminl(long double, long double)'  
```  
Am I using said header wrong or should these functions be marked `constexpr`?  
  
Here's a minimal Godbolt example that shows the issue:  
https://godbolt.org/z/9Yoz7PKoa  
  
In case this turns out to be a bug, a PR has been prepared to fix this issue: #1146
