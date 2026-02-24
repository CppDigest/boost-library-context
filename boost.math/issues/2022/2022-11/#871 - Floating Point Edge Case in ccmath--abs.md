# #871 - Floating Point Edge Case in ccmath::abs [Closed]

> Username: mborland  
> Created at: 2022-11-11 03:44:31 UTC  
> Updated at: 2022-11-13 15:25:13 UTC  
> Closed at: 2022-11-13 15:25:13 UTC  
> Url: https://github.com/boostorg/math/issues/871  

From the ML:  
  
```  
Hi Matt,  
ccmath::abs() gives wrong result for FP-values std::numeric_limits<FP-Type>::min() in constexpr context.  
  
test case:  
using type = float;  
constexpr type  
	a = std::numeric_limits<type>::min(),  
	b = boost::math::ccmath::abs(a);  
std::cout << a << std::endl << b << std::endl;  
  
1.17549e-38  
nan  
  
  
The Problem is:  
template <typename T>  
inline constexpr T abs_impl(T x) noexcept  
{  
    return boost::math::ccmath::isnan(x) ? std::numeric_limits<T>::quiet_NaN() :  
           boost::math::ccmath::isinf(x) ? std::numeric_limits<T>::infinity() :  
           x == -0 ? T(0) :  
           x == (std::numeric_limits<T>::min)() ? std::numeric_limits<T>::quiet_NaN() :  
           x > 0 ? x : -x;  
}  
  
In the case of FP-types, only the sign-bit has to be deleted, since one cannot assume that quiet_NaN() and/or signaling_NaN() are always unsigned.  
  
  
thx  
Gero  
```
