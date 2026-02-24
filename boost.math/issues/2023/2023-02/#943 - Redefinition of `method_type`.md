# #943 - Redefinition of `method_type` [Closed]

> Username: NAThompson  
> Created at: 2023-02-04 20:45:03 UTC  
> Updated at: 2023-02-05 00:51:41 UTC  
> Closed at: 2023-02-05 00:51:41 UTC  
> Url: https://github.com/boostorg/math/issues/943  

Not quite sure where this bug report should go, but here goes:  
  
```  
In file included from example/calculate_fourier_transform_daubechies_constants.cpp:4:  
In file included from /opt/homebrew/include/boost/multiprecision/cpp_bin_float.hpp:15:  
In file included from /opt/homebrew/include/boost/multiprecision/cpp_int.hpp:2367:  
In file included from /opt/homebrew/include/boost/multiprecision/cpp_int/misc.hpp:29:  
/opt/homebrew/include/boost/integer/common_factor_rt.hpp:105:12: fatal error: redefinition of 'method_type'  
      enum method_type  
           ^  
./include/boost/math/tools/polynomial_gcd.hpp:45:6: note: previous definition is here  
enum method_type  
     ^  
3 warnings and 1 error generated.  
make: *** [constants] Error 1  
```  
  
Code to reproduce (from [this PR](https://github.com/boostorg/math/pull/921)):  
  
```cpp  
#include <utility>  
#include <boost/math/filters/daubechies.hpp>  
#include <boost/math/tools/polynomial.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/math/constants/constants.hpp>  
  
using std::pow;  
using boost::multiprecision::cpp_bin_float_100;  
using boost::math::filters::daubechies_scaling_filter;  
using boost::math::tools::polynomial;  
using boost::math::constants::half;  
using boost::math::constants::root_two;  
  
template<typename Real, size_t N>  
std::vector<Real> get_constants() {  
   auto h = daubechies_scaling_filter<cpp_bin_float_100, N>();  
   auto p = polynomial<cpp_bin_float_100>(h.begin(), h.end());  
  
   auto q = polynomial({half<cpp_bin_float_100>(), half<cpp_bin_float_100>()});  
   q = pow(q, N);  
   auto l = p/q;  
   return l.data();   
}  
  
template<typename Real>  
void print_constants(std::vector<Real> const & l) {  
   std::cout << std::setprecision(std::numeric_limits<Real>::digits10 -10);  
   std::cout << "return std::array<Real, " << l.size() << ">{";  
   for (size_t i = 0; i < l.size() - 1; ++i) {  
       std::cout << "BOOST_MATH_BIG_CONSTANT(Real, std::numeric_limits<Real>::digits, " << l[i]/root_two<Real>() << "), ";  
   }  
   std::cout << "BOOST_MATH_BIG_CONSTANT(Real, std::numeric_limits<Real>::digits, " << l.back()/root_two<Real>() << ")};\n";  
}  
  
int main() {  
   auto constants = get_constants<cpp_bin_float_100, 1>();  
   print_constants(constants);  
}  
```  
  
I note that is we remove the `multiprecision` code and compile in double precision, the error goes away.

---

## Comment 1

> Username: mborland  
> Created at: 2023-02-04 21:54:26 UTC  
> Url: https://github.com/boostorg/math/issues/943#issuecomment-1416858307  

Are you building with the math standalone but the full multi-precision?

---

## Comment 2

> Username: NAThompson  
> Created at: 2023-02-04 23:56:00 UTC  
> Updated at: 2023-02-04 23:56:14 UTC  
> Url: https://github.com/boostorg/math/issues/943#issuecomment-1416879170  

@mborland : Yeah . . . undef'ing `BOOST_MATH_STANDALONE` fixed it. I think this one is on me . . .

---

## Comment 3

> Username: mborland  
> Created at: 2023-02-04 23:59:19 UTC  
> Url: https://github.com/boostorg/math/issues/943#issuecomment-1416879557  

Should I add guardrails where if one standalone is defined it automatically defines the other one?

---

## Comment 4

> Username: NAThompson  
> Created at: 2023-02-05 00:00:35 UTC  
> Url: https://github.com/boostorg/math/issues/943#issuecomment-1416879739  

@mborland : I'm not sure . . . I was only using the `-DBOOST_MATH_STANDALONE` to get rid of the `snprintf` warning from multiprecision, so I doubt this is something of huge relevance going forward.

---

## Comment 5

> Username: mborland  
> Created at: 2023-02-05 00:02:45 UTC  
> Url: https://github.com/boostorg/math/issues/943#issuecomment-1416880171  

Is the sprintf message actually coming from Multiprecision or another library? I've had the fix merged in a ton of places.

---

## Comment 6

> Username: NAThompson  
> Created at: 2023-02-05 00:04:33 UTC  
> Updated at: 2023-02-05 00:06:15 UTC  
> Url: https://github.com/boostorg/math/issues/943#issuecomment-1416880526  

Well it's because I'm consuming the rest of boost via homebrew:  
  
```  
In file included from ./include/boost/math/tools/convert_from_string.hpp:15:  
In file included from /opt/homebrew/include/boost/lexical_cast.hpp:32:  
In file included from /opt/homebrew/include/boost/lexical_cast/try_lexical_convert.hpp:44:  
In file included from /opt/homebrew/include/boost/lexical_cast/detail/converter_lexical.hpp:54:  
/opt/homebrew/include/boost/lexical_cast/detail/converter_lexical_streams.hpp:310:21: warning: 'sprintf' is deprecated: This function is provided for compatibility reasons only.  Due to security concerns inherent in the design of sprintf(3), it is highly recommended that you use snprintf(3) instead. [-Wdeprecated-declarations]  
                    sprintf(begin,  
                    ^  
```  
  
I think you do have it fixed on develop.

---

## Comment 7

> Username: mborland  
> Created at: 2023-02-05 00:08:48 UTC  
> Url: https://github.com/boostorg/math/issues/943#issuecomment-1416881317  

That one is fixed for 1.82

---

## Comment 8

> Username: NAThompson  
> Created at: 2023-02-05 00:51:41 UTC  
> Url: https://github.com/boostorg/math/issues/943#issuecomment-1416889073  

Closing as this appears to be fixed.
