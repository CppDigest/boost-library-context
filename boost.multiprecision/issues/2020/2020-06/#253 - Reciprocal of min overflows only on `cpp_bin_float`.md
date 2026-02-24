# #253 - Reciprocal of min overflows only on `cpp_bin_float` [Closed]

> Username: NAThompson  
> Created at: 2020-06-25 15:37:33 UTC  
> Updated at: 2020-06-25 17:15:21 UTC  
> Closed at: 2020-06-25 17:15:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/253  

To reproduce:  
  
```cpp  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/float128.hpp>  
#include <boost/core/demangle.hpp>  
using boost::multiprecision::cpp_bin_float_100;  
using boost::multiprecision::float128;  
  
template<typename Real>  
void print_min_reciprocal()  
{  
    auto x = std::numeric_limits<Real>::min();  
    x = 1/x;  
    std::cout << "Reciprocal of min for " << boost::core::demangle(typeid(Real).name()) << " = " << x << "\n";  
}  
  
int main() {  
    print_min_reciprocal<float>();  
    print_min_reciprocal<double>();  
    print_min_reciprocal<long double>();  
    print_min_reciprocal<float128>();  
    print_min_reciprocal<cpp_bin_float_100>();  
}  
```  
  
Output:  
  
```  
Reciprocal of min for float = 8.50706e+37  
Reciprocal of min for double = 4.49423e+307  
Reciprocal of min for long double = 2.97433e+4931  
Reciprocal of min for boost::multiprecision::number<boost::multiprecision::backends::float128_backend, (boost::multiprecision::expression_template_option)0> = 2.97433e+4931  
Reciprocal of min for boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<100u, (boost::multiprecision::backends::digit_base_type)10, void, int, 0, 0>, (boost::multiprecision::expression_template_option)0> = inf  
```  
  
Compiler is `g++-9`, if relevant.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-06-25 17:02:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/253#issuecomment-649704487  

I think this depends on the values of min/max_exponent: the types `cpp_bin_float_quad` or `cpp_bin_float_double` do not overflow in this situation, but cpp_bin_float_50/100 do as the exponent range is (very nearly) the full range of type `int`.  I note that the builtin types have asymmetric exponent ranges with -exponent_min < exponent_max.  So either we change to do something similar, or you must not make assumptions about the relative sizes of exponent_min/max.  
  
I actually suspect this may be an issue with mpfr and mpf types as well as their exponent ranges are symmetrical.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-06-25 17:05:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/253#issuecomment-649705862  

Just confirmed that this is an issue for mpfr as well (mpfr that is, not our wrapper).  IMO I don't think the assumption your making holds in general.

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-06-25 17:15:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/253#issuecomment-649711014  

>  IMO I don't think the assumption your making holds in general.  
  
Yeah, actually I wasn't sure this was a bug or not, so I think you're right.
