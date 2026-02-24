# #1139 - `evaluate_rational` should check for `abs(x) <= 1` instead of `x <= 1` when deciding to use 1/x instead of x. [Closed]

> Username: WarrenWeckesser  
> Created at: 2024-05-22 15:53:44 UTC  
> Updated at: 2024-08-02 14:44:56 UTC  
> Closed at: 2024-05-30 11:42:40 UTC  
> Url: https://github.com/boostorg/math/issues/1139  

In the detail functions for `evaluate_rational` defined in the files `rational_horner#_#.hpp` in https://github.com/boostorg/math/tree/develop/include/boost/math/tools/detail, there is code such as this:  
  
```  
template <class T, class U, class V>  
inline V evaluate_rational_c_imp(const T* a, const U* b, const V& x, const std::integral_constant<int, 6>*) BOOST_MATH_NOEXCEPT(V)  
{  
   if(x <= 1)  
     return static_cast<V>((((((a[5] * x + a[4]) * x + a[3]) * x + a[2]) * x + a[1]) * x + a[0]) / (((((b[5] * x + b[4]) * x + b[3]) * x + b[2]) * x + b[1]) * x + b[0]));  
   else  
   {  
      V z = 1 / x;  
      return static_cast<V>((((((a[0] * z + a[1]) * z + a[2]) * z + a[3]) * z + a[4]) * z + a[5]) / (((((b[0] * z + b[1]) * z + b[2]) * z + b[3]) * z + b[4]) * z + b[5]));  
   }  
}  
```  
  
I think the condition `x <= 1` should be changed to `(-1 <= x) && (x <= 1)` (i.e. `abs(x) <= 1`).  Currently, the following program prints `1 nan`, but I expect it to print `1 1`.  
  
```  
#include <iostream>  
#include "boost/math/tools/rational.hpp"  
  
int main(int argc, char *argv[])  
{  
    const double a[] = {1.0, 1.0, 1.0, 1.0, 1.0};  
    const double b[] = {1.0, 1.0, 1.0, 1.0, 1.0};  
  
    double x1 = 1e80;  
    double y1 = boost::math::tools::evaluate_rational(a, b, x1);  
  
    double x2 = -1e80;  
    double y2 = boost::math::tools::evaluate_rational(a, b, x2);  
      
    std::cout << y1 << " " << y2 << std::endl;  
  
    return 0;  
}  
```

---

## Comment 1

> Username: mborland  
> Created at: 2024-05-24 09:56:48 UTC  
> Url: https://github.com/boostorg/math/issues/1139#issuecomment-2129120859  

@jzmaddock do you have the scripts that generate these files? They all say do not edit by hand.

---

## Comment 2

> Username: WarrenWeckesser  
> Created at: 2024-05-24 11:16:38 UTC  
> Url: https://github.com/boostorg/math/issues/1139#issuecomment-2129280952  

It looks like the code generation script is here: https://github.com/boostorg/math/blob/develop/tools/generate_rational_code.cpp

---

## Comment 3

> Username: jzmaddock  
> Created at: 2024-05-24 14:33:47 UTC  
> Url: https://github.com/boostorg/math/issues/1139#issuecomment-2129677083  

> It looks like the code generation script is here: https://github.com/boostorg/math/blob/develop/tools/generate_rational_code.cpp  
  
Correct.  Matt: if you run that from within it's own directory it should put the output files in the right place.  And yes, that does indeed look like a silly bug.

---

## Comment 4

> Username: novacrazy  
> Created at: 2024-08-01 00:05:02 UTC  
> Url: https://github.com/boostorg/math/issues/1139#issuecomment-2261685444  

Sorry to resurrect this, but what is the name of the technique used here? Taking the reciprocal of X and reversing the coefficient order. I can't seem to find anything on Google.

---

## Comment 5

> Username: WarrenWeckesser  
> Created at: 2024-08-01 00:26:21 UTC  
> Url: https://github.com/boostorg/math/issues/1139#issuecomment-2261707898  

@novacrazy, it is algebra.  For example,  
  
$$  
\begin{align}  
\frac{a_0 + a_1 x + a_2 x^2}{b_0 + b_1 x + b_2 x^2}  
   &= \frac{a_0 + a_1 z^{-1} + a_2 z^{-2}}{b_0 + b_1 z^{-1} + b_2 z^{-2}} \\  
   &= \frac{a_0 z^2 + a_1 z + a_2}{b_0 z^2 + b_1 z + b_2}  \\  
   &= \frac{a_2 + a_1 z + a_0 z^2}{b_2 + b_1 z + b_0 z^2} \\  
\end{align}  
$$  
  
The steps for each line are: (1) substitute $x = 1/z$; (2) multiply top and bottom by $z^2$; (3) reorder the top and bottom expressions.  The final result is a rational function of $z$ with the coefficients in the reversed order of the original.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2024-08-02 14:37:50 UTC  
> Url: https://github.com/boostorg/math/issues/1139#issuecomment-2265547185  

There is an easier (for me anyway) way to look at this, consider:  
  
$$\frac{a_0 + a_1 x +... a_n x^n}{b_0 + b_1 x + ... b_n x^n}$$  
  
If we divide the numerator and denominator both by $$x^n$$ then we get:  
  
$$\frac{a_0(1/x)^n + a_1 x(1/x)^{n-1} +... a_n}{b_0(1/x)^n + b_1 x(1/x)^{n-1} + ... b_n}$$  
  
Now... consider the first form, it's not initially obvious but it has the limiting value of $$\frac{a_n}{b_n}$$ as x->+-INF.  However, if we attempt to evaluate that form for very large x then the x^n terms will rapidly overflow and we actually end up with INF/INF = NaN.  The second form should make the limiting value obvious, and the large powers now harmlessly underflow to zero and we smoothly approach the limiting value as x becomes large.  So the safe option is to use the second form for |x| > 1 and first for |x| < 1.  
  
So... if you're looking for a name for it, I would just call it careful programming ;)  But yes you are correct that the literature tends to just gloss over stuff like this and assume that everything is numerically stable all the time!

---

## Comment 7

> Username: novacrazy  
> Created at: 2024-08-02 14:44:55 UTC  
> Url: https://github.com/boostorg/math/issues/1139#issuecomment-2265568344  

Thanks for the answers! It helped me understand and [implement it myself](<https://github.com/novacrazy/fast_polynomial/blob/e4ca9a521c133a3a616891174c91a24d038b168d/src/lib.rs#L204-L270>).
