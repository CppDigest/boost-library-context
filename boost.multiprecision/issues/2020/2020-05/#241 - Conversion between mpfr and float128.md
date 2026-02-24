# #241 - Conversion between mpfr and float128 [Closed]

> Username: NAThompson  
> Created at: 2020-05-25 20:14:03 UTC  
> Updated at: 2020-05-25 20:18:54 UTC  
> Closed at: 2020-05-25 20:18:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/241  

I *think* there might be a conversion bug between float128 and mpfr. Code to reproduce:  
  
```cpp  
#include <boost/multiprecision/mpfr.hpp>  
#include <boost/multiprecision/float128.hpp>  
#include <boost/math/tools/ulps_plot.hpp>  
#include <boost/core/demangle.hpp>  
  
int main()  
{  
    using boost::multiprecision::number;  
    using boost::multiprecision::mpfr_float_backend;  
    using PreciseReal = number<mpfr_float_backend<300>>;  
    using CoarseReal = boost::multiprecision::float128;  
    using boost::math::tools::ulps_plot;  
    std::string filename = "rsqrt" + boost::core::demangle(typeid(CoarseReal).name()) + ".svg";  
    int samples = 10000;  
    int width = 700;  
    PreciseReal clip = 2.5;  
    auto f = [](PreciseReal x) {  
        return boost::math::rsqrt(x);  
    };  
    auto plot = ulps_plot<decltype(f), PreciseReal, CoarseReal>(f, std::numeric_limits<CoarseReal>::min(), CoarseReal(3), samples);  
    plot.clip(clip).width(width);  
    std::string title = "rsqrt ULPs plot at " + boost::core::demangle(typeid(CoarseReal).name()) + " precision";  
    plot.title(title);  
    plot.vertical_lines(6);  
    auto g = [](CoarseReal x) {  
        CoarseReal xk = 1/std::sqrt(static_cast<long double>(x));  
        // Single Newton iteration for f(x) = arg.value() - 1/x^2.  
        return  xk + xk*(1-x*xk*xk)/2;  
    };  
    plot.add_fn(g);  
    plot.write(filename);  
}  
```  
  
Error message:  
  
  
```  
In file included from ../../boost/multiprecision/number.hpp:27,  
                 from ../../boost/multiprecision/mpfr.hpp:9,  
                 from main.cpp:1:  
../../boost/multiprecision/detail/number_compare.hpp: In instantiation of 'constexpr bool boost::multiprecision::default_ops::eval_gt_imp(const T&, const U&, const false_&) [with T = boost::multiprecision::backends::float128_backend; U = boost::multiprecision::backends::mpfr_float_backend<300>; mpl_::false_ = mpl_::bool_<false>]':  
../../boost/multiprecision/detail/number_compare.hpp:93:22:   required from 'constexpr bool boost::multiprecision::default_ops::eval_gt(const T&, const U&) [with T = boost::multiprecision::backends::float128_backend; U = boost::multiprecision::backends::mpfr_float_backend<300>]'  
../../boost/multiprecision/detail/number_compare.hpp:360:18:   required from 'constexpr typename boost::enable_if_c<((boost::multiprecision::number_category<Num>::value != boost::multiprecision::number_kind_complex) && (boost::multiprecision::number_category<Backend2>::value != boost::multiprecision::number_kind_complex)), bool>::type boost::multiprecision::operator>(const boost::multiprecision::number<Backend, ExpressionTemplates>&, const boost::multiprecision::number<T2, ExpressionTemplates2>&) [with Backend = boost::multiprecision::backends::float128_backend; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off; Backend2 = boost::multiprecision::backends::mpfr_float_backend<300>; boost::multiprecision::expression_template_option ExpressionTemplates2 = boost::multiprecision::et_on; typename boost::enable_if_c<((boost::multiprecision::number_category<Num>::value != boost::multiprecision::number_kind_complex) && (boost::multiprecision::number_category<Backend2>::value != boost::multiprecision::number_kind_complex)), bool>::type = bool]'  
../../boost/math/tools/ulps_plot.hpp:114:30:   required from 'std::ostream& boost::math::tools::operator<<(std::ostream&, const boost::math::tools::ulps_plot<main()::<lambda(PreciseReal)>, boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<300> >, boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off> >&)'  
../../boost/math/tools/ulps_plot.hpp:427:8:   required from 'void boost::math::tools::ulps_plot<F, PreciseReal, CoarseReal>::write(const string&) const [with F = main()::<lambda(PreciseReal)>; PreciseReal = boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<300> >; CoarseReal = boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>; std::string = std::__cxx11::basic_string<char>]'  
main.cpp:33:24:   required from here  
../../boost/multiprecision/detail/number_compare.hpp:86:76: error: no matching function for call to 'boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<300>, boost::multiprecision::et_off>::number(const boost::multiprecision::backends::float128_backend&)'  
   86 |    typename boost::multiprecision::detail::number_from_backend<U, T>::type t(a);  
      |                                                                            ^  
compilation terminated due to -Wfatal-errors.  
make: *** [main.x] Error 1  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-05-25 20:18:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/241#issuecomment-633705231  

Nope, sry.
