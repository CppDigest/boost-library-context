# #1202 - Overflow/underflow in quaternion and octanion division operator [Open]

> Username: tk-yoshimura  
> Created at: 2024-09-18 15:16:31 UTC  
> Updated at: 2024-09-18 16:07:21 UTC  
> Url: https://github.com/boostorg/math/issues/1202  

Overflow or underflow occurs when the divisor is a giant or minute number in quaternion and octanion division.  
  
The following code, which is expected to yield 1, unexpectedly yields NaN.  
```cpp  
#include <iostream>  
#include <boost/math/quaternion.hpp>  
  
int main(){  
    boost::math::quaternion<double> q1(1e-200, 2e-200, 3e-200, 4e-200);  
    boost::math::quaternion<double> q2 = q1 / q1;  
  
    std::cout << q2 << std::endl;  
}  
```  
  
I consider exponential normalization to be necessary in the following code.  
`denominator` underflows or overflows when `rhs` is a minute or huge number.  
  
Quaternion:  
https://github.com/boostorg/math/blob/c3afa49c9a66523bd9be47888088337d0229a7cb/include/boost/math/quaternion.hpp#L398-L420  
  
Octanion:  
https://github.com/boostorg/math/blob/c3afa49c9a66523bd9be47888088337d0229a7cb/include/boost/math/octonion.hpp#L561-L654

---

## Comment 1

> Username: mborland  
> Created at: 2024-09-18 15:33:37 UTC  
> Url: https://github.com/boostorg/math/issues/1202#issuecomment-2358800729  

While this should get fixed have you tried Boost.QVM (https://www.boost.org/doc/libs/1_86_0/libs/qvm/doc/html/index.html)? I believe it has a much richer set of support than our quaternions (which is the oldest part of the library)

---

## Comment 2

> Username: tk-yoshimura  
> Created at: 2024-09-18 16:07:19 UTC  
> Url: https://github.com/boostorg/math/issues/1202#issuecomment-2358874929  

Thank you for sharing about QVM.  
  
Here is how I discovered this problem.  
While implementing complex Bessel functions, I encountered a similar problem with the division of the micro-norm.  
I verified that boost does not have a similar flaw and reported it because it seems to have the same mistake in quaternion and octonion.
