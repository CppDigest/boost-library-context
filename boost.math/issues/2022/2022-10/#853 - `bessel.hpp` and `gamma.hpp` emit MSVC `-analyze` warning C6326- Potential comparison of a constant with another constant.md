# #853 - `bessel.hpp` and `gamma.hpp` emit MSVC `/analyze` warning C6326: Potential comparison of a constant with another constant. [Closed]

> Username: StephanTLavavej  
> Created at: 2022-10-25 01:32:34 UTC  
> Updated at: 2022-10-27 17:04:28 UTC  
> Closed at: 2022-10-27 17:04:28 UTC  
> Url: https://github.com/boostorg/math/issues/853  

https://godbolt.org/z/jdd9b6ene  
  
```cpp  
#include <boost/math/special_functions/bessel.hpp>  
  
int main() {  
    (void) boost::math::cyl_bessel_i(0.0, 0.0);  
}  
```  
  
When compiled with MSVC `/analyze`, this warns:  
  
```  
[...]\include\boost\math\special_functions\bessel.hpp(206) : warning C6326: Potential comparison of a constant with another constant.  
[...]\include\boost\math\special_functions\gamma.hpp(258) : warning C6326: Potential comparison of a constant with another constant.  
```  
  
These lines are indeed testing compile-time constants:  
  
https://github.com/boostorg/math/blob/a8f0f0a1e24732ac0fde42593d8311a0b6cbd336/include/boost/math/special_functions/bessel.hpp#L206  
https://github.com/boostorg/math/blob/a8f0f0a1e24732ac0fde42593d8311a0b6cbd336/include/boost/math/special_functions/gamma.hpp#L258  
  
This warning is merely an annoyance, nothing is wrong with the code, so it should simply be silenced (e.g. with push-disable-pop).
