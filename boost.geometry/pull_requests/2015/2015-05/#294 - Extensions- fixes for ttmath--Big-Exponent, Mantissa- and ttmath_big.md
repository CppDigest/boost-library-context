# #294 Extensions: fixes for ttmath::Big<Exponent, Mantissa> and ttmath_big [Merged]

> Username: mkaravel  
> Created at: 2015-05-11 09:45:21 UTC  
> Updated at: 2015-06-12 07:05:07 UTC  
> Merged at: 2015-06-12 07:05:07 UTC  
> Closed at: 2015-06-12 07:05:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/294  

Consider the following two programs.  
#### Program 1  
  
```  
#include <boost/geometry/extensions/contrib/ttmath_stub.hpp>  
  
template <typename T>  
T f(T const& x)  
{  
    return T(2.0) * cos(x);  
}  
  
  
int main()  
{  
    ttmath_big x = 0.5;  
    f(x);  
  
    return 0;  
}  
```  
#### Program 2  
  
```  
#include <boost/assert.hpp>  
  
#include <boost/geometry/util/math.hpp>  
#include <boost/geometry/extensions/contrib/ttmath_stub.hpp>  
  
namespace math = boost::geometry::math;  
  
int main()  
{  
    ttmath_big half_pi = math::half_pi<ttmath_big>();  
    ttmath_big deg = 90;  
    ttmath_big d2r = math::pi<ttmath_big>() / ttmath_big(180);  
    ttmath_big rad = deg * d2r;  
  
    BOOST_ASSERT(math::equals(half_pi, rad));  
  
    return 0;  
}  
```  
#### Analysis for Program 1  
  
The first program fails at compile time. The reason is that the currently implemented `ttmath`-related math functions are implemented only for `ttmath::Big<Exponent, Mantissa>` and return a value of the same type. When `operator*()` is called within function `f()` it tries to multiply an object of type `ttmath_big` and an object of type `ttmath::Big<Exponent, Mantissa>` and the compiler cannot choose between the two available overloads for `operator*()`:  
  
```  
ttmath_big operator*(ttmath_big const&, ttmath_big const&)`  
```  
  
and  
  
```  
ttmath::Big<E, M> operator*(ttmath::Big<E, M> const&, ttmath::Big<E, M> const&)  
```  
  
**_Solution:**_ In this PR we explicitly define all math function for `ttmath_big` (which obviously return an object of type `ttmath_big`); this way both operands of `operator*()` in `f()` are of the same type and the ambiguity is resolved.  
#### Analysis for Program 2  
  
When `math::equals()` is called the two values are checked without any use of precision tolerance. Due to the nature of floating-point operations (and the fact that division may not be exact, as in this case), the two operands of `math::equals` do not have _exactly_ the same value, and the check fails.  
  
Since `ttmath_big` and `ttmath::Big<Exponent, Mantissa>` are fixed-precision floating-point numbers it is very natural to expect this kind of truncation errors. As a result, it makes more sense to check equality of two values given some machine epsilon, which is what this PR does.  
  
**_Solution:**_ In this PR `math::equals` is specialized for `ttmath_big` and `ttmath::Big<Exponent, Mantissa>` so that equality check is performed using some epsilon. The current tolerance used is `std::numeric_limits<double>::epsilon()`, but at a future time it should be changed to something more accurate for these number types.

---
