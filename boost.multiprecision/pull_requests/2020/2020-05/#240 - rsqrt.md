# #240 rsqrt [Merged]

> Username: NAThompson  
> Created at: 2020-05-25 18:45:51 UTC  
> Updated at: 2020-05-31 02:42:26 UTC  
> Merged at: 2020-05-31 02:34:04 UTC  
> Closed at: 2020-05-31 02:34:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/240  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-05-25 20:45:59 UTC  
> Updated_at: 2020-05-30 15:09:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/240#issuecomment-633711724  

In any case, the following ulps plot show they both work:  
  
```cpp  
#include <boost/multiprecision/mpfr.hpp>  
#include <boost/multiprecision/float128.hpp>  
#include <boost/math/tools/ulps_plot.hpp>  
  
int main()  
{  
    using boost::multiprecision::number;  
    using PreciseReal =number<boost::multiprecision::mpfr_float_backend<400>>;  
    using CoarseReal = boost::multiprecision::float128;  
    using boost::math::tools::ulps_plot;  
    std::string filename = "rsqrt_quad_03.svg";  
    int samples = 10000;  
    // How many pixels wide do you want your .svg?  
    int width = 1400;  
    // Near a root, we have unbounded relative error. So for functions with roots, we define an ULP clip:  
    PreciseReal clip = 2.5;  
    auto f = [](PreciseReal x) {  
        using boost::math::rsqrt;  
        return rsqrt(x);  
    };  
    auto plot03 = ulps_plot<decltype(f), PreciseReal, CoarseReal>(f, std::numeric_limits<CoarseReal>::min(), CoarseReal(3), samples);  
    plot03.clip(clip).width(width);  
    std::string title = "rsqrt ULPs plot at quad precision";  
    plot03.title(title);  
    plot03.vertical_lines(6);  
    auto g = [](CoarseReal x) {  
        CoarseReal xk = 1/std::sqrt(static_cast<long double>(x));  
        // Single Newton iteration for f(x) = arg.value() - 1/x^2.  
        return  xk + xk*(1-x*xk*xk)/2;  
    };  
    plot03.add_fn(g);  
  
    // Two Newton iterates:  
    auto g2 = [](CoarseReal x) {  
        CoarseReal xk = 1/std::sqrt(static_cast<long double>(x));  
        xk = xk + xk*(1-x*xk*xk)/2;  
        return xk + xk*(1-x*xk*xk)/2;  
    };  
    plot03.add_fn(g2, "orange");  
    plot03.write(filename);  
  
    filename = "rsqrt_quad_3_100.svg";  
  
    auto plot3_100 = ulps_plot<decltype(f), PreciseReal, CoarseReal>(f, CoarseReal(3), CoarseReal(100), samples);  
    plot3_100.clip(clip).width(width);  
    plot3_100.title(title);  
    plot3_100.vertical_lines(6);  
    plot3_100.add_fn(g);  
    plot3_100.add_fn(g2, "orange");  
    plot3_100.write(filename);  
}  
```  
  
And here's the result we have on `[0,3]`:  
  
<img width="1387" alt="Screen Shot 2020-05-25 at 4 44 41 PM" src="https://user-images.githubusercontent.com/5459618/82842317-0e029a00-9ea7-11ea-91f9-0caaffb10e95.png">  
  
<img width="1392" alt="Screen Shot 2020-05-25 at 4 45 06 PM" src="https://user-images.githubusercontent.com/5459618/82842341-21ae0080-9ea7-11ea-97b0-3d23d31c4fb5.png">  
  
Performance (assuming an 80-bit long double):  
  
```  
RSqrtBM<boost::multiprecision::float128>                                                       135 ns          135 ns      5158894  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<100>>>         434 ns          434 ns      1611474  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<200>>>         584 ns          584 ns      1203473  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<300>>>         782 ns          781 ns       890370  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<400>>>        1019 ns         1018 ns       697760  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<500>>>        1266 ns         1265 ns       556572  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<1000>>>       3285 ns         3283 ns       212634  
```  
  
Compared to `1/sqrt(x)`:  
  
```  
RSqrtBM<boost::multiprecision::float128>                                                       314 ns          314 ns      2227987  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<100>>>         594 ns          594 ns      1177262  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<200>>>         814 ns          814 ns       871970  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<300>>>        1077 ns         1076 ns       667684  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<400>>>        1296 ns         1296 ns       530576  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<500>>>        1521 ns         1520 ns       470124  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<1000>>>       3670 ns         3669 ns       190175  
```

---

## Comment 2

> Username: NAThompson  
> Created_at: 2020-05-30 15:13:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/240#issuecomment-636343977  

Ok, I'm a bit confused about where everything should be documented and tested. Normally this would be implemented in boost.math, and I'd put the ulps plot in the documentation there. But this is a kinda weird situation where it's half part of the `mpfr_wrapper`, half custom implementation.  
  
Should I do another PR in boost.math? That would also get `rsqrt` into `float`, `double`, and `long double` precision.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2020-05-30 19:21:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/240#issuecomment-636373348  

>Ok, I'm a bit confused about where everything should be documented and tested. Normally this would be implemented in boost.math, and I'd put the ulps plot in the documentation there. But this is a kinda weird situation where it's half part of the mpfr_wrapper, half custom implementation.  
  
>Should I do another PR in boost.math? That would also get rsqrt into float, double, and long double precision.  
  
What I've done previously is provide the main/generic implementtion in boost/math/special_functions/ and then overload the function in multiprecision as required.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2020-05-30 19:22:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/240#issuecomment-636373394  

Ooops, forgot to say, this of course changes the namespace that rsqrt is declared in.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2020-05-31 01:44:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/240#issuecomment-636408230  

@jzmaddock : Yup, just validated that this works and does indeed pick up the right version of the file. Tests in boost.math.

---
