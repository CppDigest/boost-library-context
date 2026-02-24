# #1006 - bug in boost libraries used by scipy.stats.beta [Closed]

> Username: Gabriel-Kissin  
> Created at: 2023-07-30 10:05:45 UTC  
> Updated at: 2023-09-02 08:14:55 UTC  
> Closed at: 2023-09-02 08:14:55 UTC  
> Url: https://github.com/boostorg/math/issues/1006  

I've found a bug in a couple of python `scipy` probability functions which leads to the kernel crashing. In the case of the `beta` function, the error originates in the source in boost which appears to be [at boost.org](https://www.boost.org/doc/libs/1_82_0/boost/math/special_functions/beta.hpp) and [on github](https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/beta.hpp) in one of the lines reading `BOOST_MATH_ASSERT(*p_derivative >= 0)`.     
  
I originally posted this issue at https://github.com/scipy/scipy/issues/18906. But am now reposting the python code here as requested there by @jzmaddock.     
  
  
```  
import scipy.stats  
print([shape_param.domain for shape_param in scipy.stats.beta._shape_info()])  
# [[5e-324, inf], [5e-324, inf]]  
print(scipy.stats.beta(a=1e-308, b=5).ppf(.2))  
# 0.0  
print(scipy.stats.beta(a=1e-309, b=5).ppf(.2))  
# OverflowError: Error in function boost::math::tgamma<d>(d): Overflow Error  
# (raises error, but kernel doesn't crash)  
print(scipy.stats.beta(a=1e-323, b=5).ppf(.2))  
# KERNEL CRASHES with message: `Assertion failed: *p_derivative >= 0, file    
 ..\..\scipy\_lib\boost/boost/math/special_functions/beta.hpp, line 739`  
```
