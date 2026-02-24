# #1347 - How to use autodiff with std::complex [Open]

> Username: xuruilong100  
> Created at: 2026-01-07 09:13:50 UTC  
> Updated at: 2026-02-04 02:31:29 UTC  
> Url: https://github.com/boostorg/math/issues/1347  

Can autodiff work with `std::complex`? And how to use?  
  
Is there someone shows me an example?

---

## Comment 1

> Username: pulver  
> Created at: 2026-01-07 14:18:34 UTC  
> Url: https://github.com/boostorg/math/issues/1347#issuecomment-3719098164  

> Can autodiff work with std::complex?  
  
Not with forward autodiff, as it's currently written. One primary reason is that the [field](https://en.wikipedia.org/wiki/Field_(mathematics)) must be well-ordered, and the complex field is not. Attempting to forward-autodiff with `std::complex<double>` for example results in compiler errors due to `operator<()` not being defined for it, which the library currently requires and makes use of.

---

## Comment 2

> Username: demroz  
> Created at: 2026-01-29 22:38:17 UTC  
> Updated at: 2026-01-29 23:06:21 UTC  
> Url: https://github.com/boostorg/math/issues/1347#issuecomment-3820785651  

Sorry for the late reply, I didn't see this until now  
  
> Can autodiff work with `std::complex`? And how to use?  
>   
> Is there someone shows me an example?  
  
This is currently not supported with reverse mode either, but may come in the future. Problem is the `tape` would have to be rework to support non-holomorphic functions.   
  
---------------------------------------------------------------------------------------------------------------------------------------------------------------------  
  
There is kind of a hack to this. If you change line 312 in `autodiff_reverse.hpp`  
```  
if (!adjoint_  || fabs(*adjoint_) < 2.0 * std::numeric_limits<RealType>::epsilon())  
```  
to  
  
```  
if (!adjoint_ )  
```  
the holomorphic functions should produce correct derivatives. i.e. :   
  
```cpp  
#include <boost/math/differentiation/autodiff_reverse.hpp>  
#include <boost/math/optimization/gradient_descent.hpp>  
#include <boost/math/optimization/minimizer.hpp>  
#include <cmath>  
#include <fstream>  
#include <iostream>  
#include <random>  
#include <string>  
namespace rdiff = boost::math::differentiation::reverse_mode;  
namespace bopt  = boost::math::optimization;  
  
int main()  
{  
    std::complex<double> xcd(1.0+2.0i);  
    std::complex<double> ycd(3.0+4.0i);  
  
    rdiff::rvar<std::complex<double>,1> x(xcd);  
    rdiff::rvar<std::complex<double>,1> y(ycd);  
    rdiff::rvar<std::complex<double>,1> z = sin(x*x+y);  
    z.backward();  
    std::complex<double> expected_ddx = 2.0 * xcd * cos(xcd*xcd +  ycd);  
    std::complex<double> expected_ddy=   cos(xcd*xcd + ycd);  
    std::cout<<"result = " << sin(xcd*xcd + ycd) << " aad result " << z.item() << std::endl; //<< " " << x << " " << y << std::endl;  
    std::cout << "d/dx expected = " << expected_ddx << " aad result " << x.adjoint() << std::endl;  
    std::cout << "d/dy expected = " << expected_ddy << " aad result " << y.adjoint() << std::endl;  
  
    return 0;  
}  
```  
seems to produce the correct results  
```  
result = (0,1490.48) aad result (0,1490.48)  
d/dx expected = (2980.96,5961.92) aad result (2980.96,5961.92)  
d/dy expected = (1490.48,-0) aad result (1490.48,0)  
```  
  
but again : this is not tested code, and I don't officially support this.

---

## Comment 3

> Username: xuruilong100  
> Created at: 2026-02-04 02:31:29 UTC  
> Url: https://github.com/boostorg/math/issues/1347#issuecomment-3844893144  

```cpp  
template <Size ORDER, Size... ORDERS>  
using Fvar =  
    boost::math::differentiation::autodiff_fvar<Real, ORDER, ORDERS...>;  
  
using Fvar1 = Fvar<1>;  
using Fvar2 = Fvar<2>;  
using Fvar1_1 = Fvar<1, 1>;  
```  
  
After a long time debuging, the fact tells me that `std::complex<Fvar1>` is not a good idea. Finally, I created `FComplex`  
  
```cpp  
template <typename FVAR>  
class FComplex{  
public:  
 using value_type = FVAR;  
 using this_type = typename FComplex<FVAR>;  
  
...  
  
private:  
 value_type re_{0.0};  
 value_type im_{0.0};  
};  
```  
  
And it works well, it makes my Heston pricing formula differentiability.
