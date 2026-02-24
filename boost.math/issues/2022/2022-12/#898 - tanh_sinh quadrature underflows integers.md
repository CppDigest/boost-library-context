# #898 - tanh_sinh quadrature underflows integers [Closed]

> Username: gphlipot13  
> Created at: 2022-12-09 13:13:32 UTC  
> Updated at: 2022-12-11 16:27:42 UTC  
> Closed at: 2022-12-11 16:27:42 UTC  
> Url: https://github.com/boostorg/math/issues/898  

# Description  
It is possible for some integrals (and some scalar types), the tanh_sinh quadrature method underflows an unsigned integer, resulting in undefined behavior.  
  
# Steps to reproduce   
See an example where the https://godbolt.org/z/5seaesETo  
  
The integer underflow occurs on [this line](https://github.com/boostorg/math/blob/develop/include/boost/math/quadrature/detail/tanh_sinh_detail.hpp#L352) when `max_left_position` is 0.
