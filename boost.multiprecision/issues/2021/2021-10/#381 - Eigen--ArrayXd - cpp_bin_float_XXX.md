# #381 - Eigen::ArrayXd * cpp_bin_float_XXX [Closed]

> Username: ianhbell  
> Created at: 2021-10-13 19:49:20 UTC  
> Updated at: 2021-12-04 12:26:08 UTC  
> Closed at: 2021-12-04 12:26:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/381  

Related to #374, I would like to multiply an ``Eigen::ArrayXd`` by an extended precision scalar, in order to obtain an ``Eigen::ArrayX<cpp_bin_float_50>``, but that doesn't seem to be supported.  I tried:  
  
``` c++  
#include <Eigen/Dense>  
  
// Imports from boost  
#include <boost/multiprecision/cpp_bin_float.hpp>  
using my_float = boost::multiprecision::cpp_bin_float_50;  
  
// Some array of doubles  
Eigen::ArrayXd a = Eigen::ArrayXd::LinSpaced(30, 1, 34);  
  
// Our extended precision number  
my_float b = 2.3;  
  
// Product of double array times extended precision scalar  
auto c = b * a;  
```  
with boost 1.77 & eigen 3.4.0  
  
All the compilers I have tried thus far yield some variation on:  
```  
Output of x86-64 clang (trunk) (Compiler #2)  
<source>:14:12: error: invalid operands to binary expression ('my_float' (aka 'number<backends::cpp_bin_float<50>>') and 'Eigen::ArrayXd' (aka 'Array<double, Dynamic, 1>'))  
auto c = b * a;  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-12-04 12:26:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/381#issuecomment-986019280  

I think you need to check with Eigen on this, but it appears not to be supported:  I *think* multiplication of a vector by a scalar is supported only if the scalar is implicitly convertible to the vectors value_type.  So for example multiplying a vector of doubles by a long double results in a vector of double (ie potential loss of precision), and the matrix/vector/array type never changes type as a result of multiplication by a scalar.  
  
Please reopen if I've missed something and there is a way to do this.
