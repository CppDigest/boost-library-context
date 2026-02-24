# #697 - Ambiguous call of ldexp in standalone_test_arithmetic_cpp_rational.cpp in generic_interconvert [Closed]

> Username: JkktBkkt  
> Created at: 2025-06-19 12:51:26 UTC  
> Updated at: 2026-02-09 20:18:18 UTC  
> Closed at: 2026-02-09 20:18:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/697  

Building version 1.83 with gcc14.2, this test test errors with:  
Am I missing some relevant change?  
```  
./boost/multiprecision/detail/generic_interconvert.hpp:375:18: error: call of overloaded 'ldexp(__float128&, int)' is ambiguous  
  375 |    result = ldexp(result, static_cast<int>(-shift));  
      |             ~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from libs/multiprecision/test/test.hpp:11:  
/usr/include/c++/14.2/cmath:311:3: note: candidate: 'constexpr long double std::ldexp(long double, int)'  
  311 |   ldexp(long double __x, int __exp)  
      |   ^~~~~  
/usr/include/c++/14.2/cmath:307:3: note: candidate: 'constexpr float std::ldexp(float, int)'  
  307 |   ldexp(float __x, int __exp)  
      |   ^~~~~  
In file included from /usr/include/features.h:524,  
                 from /usr/include/c++/14.2/x86_64-unknown-linux-gnu/bits/os_defines.h:39,  
                 from /usr/include/c++/14.2/x86_64-unknown-linux-gnu/bits/c++config.h:683,  
                 from /usr/include/c++/14.2/functional:48,  
                 from libs/multiprecision/test/test_arithmetic.hpp:10:  
/usr/include/bits/mathcalls.h:119:1: note: candidate: 'double ldexp(double, int)'  
  119 | __MATHCALL (ldexp,, (_Mdouble_ __x, int __exponent));  
      | ^~~~~~~~~~  
```  
  
<details>  
  
```  
=> Testing: boost::multiprecision  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
    - cxx11_rvalue_references  : yes (cached) [2]  
    - cxx11_template_aliases   : yes (cached) [2]  
    - cxx11_hdr_array          : yes (cached) [2]  
    - cxx11_allocator          : yes (cached) [2]  
    - cxx11_constexpr          : yes (cached) [2]  
    - cxx11_explicit_conversion_operators : yes (cached) [2]  
    - cxx11_ref_qualifiers     : yes (cached) [2]  
    - cxx11_hdr_functional     : yes (cached) [2]  
    - cxx11_variadic_templates : yes (cached) [2]  
    - cxx11_user_defined_literals : yes (cached) [2]  
    - cxx11_decltype           : yes (cached) [2]  
    - cxx11_static_assert      : yes (cached) [2]  
    - cxx11_defaulted_functions : yes (cached) [2]  
    - cxx11_noexcept           : yes (cached) [2]  
    - cxx11_hdr_type_traits    : yes (cached) [2]  
    - cxx14_decltype_auto      : yes (cached) [2]  
    - cxx14_generic_lambdas    : yes (cached) [2]  
    - cxx14_return_type_deduction : yes (cached) [2]  
    - cxx14_variable_templates : yes (cached) [2]  
    - cxx11_rvalue_references  : yes (cached) [3]  
    - cxx11_template_aliases   : yes (cached) [3]  
    - cxx11_hdr_array          : yes (cached) [3]  
    - cxx11_allocator          : yes (cached) [3]  
    - cxx11_constexpr          : yes (cached) [3]  
    - cxx11_explicit_conversion_operators : yes (cached) [3]  
    - cxx11_ref_qualifiers     : yes (cached) [3]  
    - cxx11_hdr_functional     : yes (cached) [3]  
    - cxx11_variadic_templates : yes (cached) [3]  
    - cxx11_user_defined_literals : yes (cached) [3]  
    - cxx11_decltype           : yes (cached) [3]  
    - cxx11_static_assert      : yes (cached) [3]  
    - cxx11_defaulted_functions : yes (cached) [3]  
    - cxx11_noexcept           : yes (cached) [3]  
    - cxx11_hdr_type_traits    : yes (cached) [3]  
    - cxx14_decltype_auto      : yes (cached) [3]  
    - cxx14_generic_lambdas    : yes (cached) [3]  
    - cxx14_return_type_deduction : yes (cached) [3]  
    - cxx14_variable_templates : yes (cached) [3]  
    - has_float128 builds      : yes (cached) [3]  
    - has_gmp builds           : no  (cached) [3]  
    - cxx11_rvalue_references  : yes (cached) [4]  
    - cxx11_template_aliases   : yes (cached) [4]  
    - cxx11_hdr_array          : yes (cached) [4]  
    - cxx11_allocator          : yes (cached) [4]  
    - cxx11_constexpr          : yes (cached) [4]  
    - cxx11_explicit_conversion_operators : yes (cached) [4]  
    - cxx11_ref_qualifiers     : yes (cached) [4]  
    - cxx11_hdr_functional     : yes (cached) [4]  
    - cxx11_variadic_templates : yes (cached) [4]  
    - cxx11_user_defined_literals : yes (cached) [4]  
    - cxx11_decltype           : yes (cached) [4]  
    - cxx11_static_assert      : yes (cached) [4]  
    - cxx11_defaulted_functions : yes (cached) [4]  
    - cxx11_noexcept           : yes (cached) [4]  
    - cxx11_hdr_type_traits    : yes (cached) [4]  
    - cxx14_decltype_auto      : yes (cached) [4]  
    - cxx14_generic_lambdas    : yes (cached) [4]  
    - cxx14_return_type_deduction : yes (cached) [4]  
    - cxx14_variable_templates : yes (cached) [4]  
    - has_gmp builds           : no  (cached) [4]  
    - has_mpfr builds          : no  (cached) [3]  
    - has_mpfr builds          : no  (cached) [4]  
    - has_tommath builds       : no  (cached) [3]  
    - has_tommath builds       : no  (cached) [4]  
    - has_mpc builds           : no  (cached) [3]  
    - has_mpc builds           : no  (cached) [4]  
    - has_mpfi builds          : no  (cached) [3]  
    - has_mpfi builds          : no  (cached) [4]  
    - has_intel_quad builds    : no  (cached) [3]  
    - has_intel_quad builds    : no  (cached) [4]  
    - cxx17_if_constexpr       : yes (cached) [3]  
    - cxx17_if_constexpr       : yes (cached) [4]  
    - has_float128 builds      : yes (cached) [4]  
    - Sanitizer CI run         : no  (cached) [3]  
    - Sanitizer CI run         : no  (cached) [4]  
    - has std::atomic_ref      : no  (cached) [3]  
    - has -Wl,--no-undefined   : yes (cached) [3]  
    - has statx                : yes (cached) [3]  
    - has init_priority attribute : yes (cached) [3]  
    - has stat::st_blksize     : yes (cached) [3]  
    - has stat::st_mtim        : yes (cached) [3]  
    - has stat::st_mtimensec   : no  (cached) [3]  
    - has stat::st_mtimespec   : no  (cached) [3]  
    - has stat::st_birthtim    : no  (cached) [3]  
    - has stat::st_birthtimensec : no  (cached) [3]  
    - has stat::st_birthtimespec : no  (cached) [3]  
    - has fdopendir(O_NOFOLLOW) : yes (cached) [3]  
    - has dirent::d_type       : yes (cached) [3]  
    - has POSIX *at APIs       : yes (cached) [3]  
    - compiler supports SSE2   : yes (cached) [3]  
    - compiler supports SSE4.1 : yes (cached) [3]  
    - cxx11_hdr_random         : yes (cached) [3]  
    - cxx14_constexpr          : yes (cached) [3]  
    - has_is_constant_evaluated builds : yes (cached) [3]  
    - has_constexpr_limits builds : yes (cached) [3]  
    - cpp_lib_bit_cast         : no  (cached) [3]  
    - cpp_lib_bit_cast         : no  (cached) [4]  
    - has_eigen builds         : no  (cached) [3]  
    - has_eigen builds         : no  (cached) [4]  
    - has_gmp builds           : no  (cached) [2]  
    - has_mpfr builds          : no  (cached) [2]  
    - has_mpfr builds          : no  (cached) [5]  
    - has_mpfr builds          : no  (cached) [6]  
    - has_gmp builds           : no  (cached) [5]  
    - has_gmp builds           : no  (cached) [6]  
    - cxx11_rvalue_references  : yes (cached) [5]  
    - cxx11_template_aliases   : yes (cached) [5]  
    - cxx11_hdr_array          : yes (cached) [5]  
    - cxx11_allocator          : yes (cached) [5]  
    - cxx11_constexpr          : yes (cached) [5]  
    - cxx11_explicit_conversion_operators : yes (cached) [5]  
    - cxx11_ref_qualifiers     : yes (cached) [5]  
    - cxx11_hdr_functional     : yes (cached) [5]  
    - cxx11_variadic_templates : yes (cached) [5]  
    - cxx11_user_defined_literals : yes (cached) [5]  
    - cxx11_decltype           : yes (cached) [5]  
    - cxx11_static_assert      : yes (cached) [5]  
    - cxx11_defaulted_functions : yes (cached) [5]  
    - cxx11_noexcept           : yes (cached) [5]  
    - cxx11_hdr_type_traits    : yes (cached) [5]  
    - cxx14_decltype_auto      : yes (cached) [5]  
    - cxx14_generic_lambdas    : yes (cached) [5]  
    - cxx14_return_type_deduction : yes (cached) [5]  
    - cxx14_variable_templates : yes (cached) [5]  
    - has_big_obj builds       : no  (cached) [3]  
    - cxx11_lambdas            : yes (cached) [3]  
    - cxx11_nullptr            : yes (cached) [3]  
    - cxx11_numeric_limits     : yes (cached) [3]  
    - BOOST_COMP_GNUC >= 4.3.0 : yes (cached) [7]  
    - cxx11_hdr_atomic         : yes (cached) [3]  
    - cxx11_hdr_thread         : yes (cached) [3]  
    - cxx11_hdr_atomic         : yes (cached) [4]  
    - cxx11_hdr_thread         : yes (cached) [4]  
    - cxx11_auto_declarations  : yes (cached) [3]  
    - lockfree boost::atomic_flag : yes (cached) [3]  
    - has_f2c builds           : no  (cached) [3]  
    - has_f2c builds           : no  (cached) [4]  
  
[1] gcc-14  
[2] gcc-14/release/architecture-x86/python-3.13/threading-multi/visibility-hidden  
[3] gcc-14/release/python-3.13/threading-multi/visibility-hidden  
[4] gcc-14/release/build-no/python-3.13/threading-multi/visibility-hidden  
[5] gcc-14/release/optimization-space/python-3.13/threading-multi/visibility-hidden  
[6] gcc-14/release/build-no/optimization-space/python-3.13/threading-multi/visibility-hidden  
[7] gcc-14/release/link-static/python-3.13/threading-multi/visibility-hidden  
...patience...  
...patience...  
...patience...  
...patience...  
...found 10034 targets...  
...updating 4 targets...  
gcc.compile.c++ bin.v2/libs/multiprecision/test/standalone_test_arithmetic_cpp_rational.test/gcc-14/release/threading-multi/visibility-hidden/standalone_test_arithmetic_cpp_rational.o  
In file included from ./boost/multiprecision/detail/generic_interconvert.hpp:12,  
                 from ./boost/multiprecision/number.hpp:12,  
                 from libs/multiprecision/test/test.hpp:16,  
                 from libs/multiprecision/test/test_arithmetic.hpp:16,  
                 from libs/multiprecision/test/standalone_test_arithmetic_cpp_rational.cpp:9:  
./boost/multiprecision/detail/default_ops.hpp: In instantiation of 'constexpr typename boost::multiprecision::scalar_result_from_possible_complex<typename boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type>::type boost::multiprecision::norm(const detail::expression<tag, Arg1, Arg2, Arg3, Arg4>&) [with tag = detail::multiply_immediates; A1 = number<backends::rational_adaptor<backends::cpp_int_backend<> > >; A2 = int; A3 = void; A4 = void; typename scalar_result_from_possible_complex<typename detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type>::type = number<backends::rational_adaptor<backends::cpp_int_backend<> > >; typename detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type = number<backends::rational_adaptor<backends::cpp_int_backend<> > >]':  
libs/multiprecision/test/test_arithmetic.hpp:2870:4:   required from 'typename std::enable_if<(boost::multiprecision::number_category<Num>::value != boost::multiprecision::number_kind_complex)>::type test_members(Real) [with Real = boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >; typename std::enable_if<(boost::multiprecision::number_category<Num>::value != boost::multiprecision::number_kind_complex)>::type = void]'  
 2870 |    BOOST_CHECK_EQUAL(norm(a * 1), a * a);  
libs/multiprecision/test/test_arithmetic.hpp:3391:16:   required from 'void test() [with Real = boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >]'  
 3391 |    test_members(a);  
      |    ~~~~~~~~~~~~^~~  
libs/multiprecision/test/standalone_test_arithmetic_cpp_rational.cpp:35:45:   required from here  
   35 |    test<boost::multiprecision::cpp_rational>();  
      |    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~  
./boost/multiprecision/detail/default_ops.hpp:2366:54: warning: moving a temporary object prevents copy elision [-Wpessimizing-move]  
 2366 |    return std::move(norm(static_cast<number_type>(v)));  
      |                                                      ^  
./boost/multiprecision/detail/default_ops.hpp:2366:54: note: remove 'std::move' call  
./boost/multiprecision/detail/generic_interconvert.hpp: In instantiation of 'typename std::enable_if<(boost::multiprecision::is_number<T>::value || std::is_floating_point<_Tp>::value)>::type boost::multiprecision::detail::generic_convert_rational_to_float_imp(To&, Integer&, Integer&, const std::integral_constant<bool, false>&) [with To = __float128; Integer = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >; typename std::enable_if<(boost::multiprecision::is_number<T>::value || std::is_floating_point<_Tp>::value)>::type = void]':  
./boost/multiprecision/detail/generic_interconvert.hpp:404:41:   required from 'void boost::multiprecision::detail::generic_convert_rational_to_float(To&, const From&) [with To = __float128; From = boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> >]'  
  404 |    generic_convert_rational_to_float_imp(result, n, d, dispatch_tag());  
      |    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
./boost/multiprecision/rational_adaptor.hpp:393:70:   required from 'typename std::enable_if<(boost::multiprecision::number_category<Num>::value == boost::multiprecision::number_kind_floating_point)>::type boost::multiprecision::backends::eval_convert_to(R*, const rational_adaptor<IntBackend>&) [with R = __float128; IntBackend = cpp_int_backend<>; typename std::enable_if<(boost::multiprecision::number_category<Num>::value == boost::multiprecision::number_kind_floating_point)>::type = void]'  
  393 |    ::boost::multiprecision::detail::generic_convert_rational_to_float(*result, backend);  
      |    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~  
./boost/multiprecision/number.hpp:1046:22:   required from 'constexpr void boost::multiprecision::number<Backend, ExpressionTemplates>::convert_to_imp(T*) const [with T = __float128; Backend = boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> >; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_on]'  
 1046 |       eval_convert_to(result, m_backend);  
      |       ~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~  
./boost/multiprecision/number.hpp:1063:21:   required from 'constexpr T boost::multiprecision::number<Backend, ExpressionTemplates>::convert_to() const [with T = __float128; Backend = boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> >; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_on]'  
 1063 |       convert_to_imp(&result);  
      |       ~~~~~~~~~~~~~~^~~~~~~~~  
libs/multiprecision/test/test_arithmetic.hpp:2253:4:   required from 'void test_mixed(const std::integral_constant<bool, true>&) [with Real = boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >; Num = __float128]'  
 2253 |    BOOST_CHECK_EQUAL(Real(n1).template convert_to<Num>(), n1);  
libs/multiprecision/test/test_arithmetic.hpp:3216:35:   required from 'void test() [with Real = boost::multiprecision::number<boost::multiprecision::backends::rational_adaptor<boost::multiprecision::backends::cpp_int_backend<> > >]'  
 3216 |       test_mixed<Real, __float128>(tag);  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~  
libs/multiprecision/test/standalone_test_arithmetic_cpp_rational.cpp:35:45:   required from here  
   35 |    test<boost::multiprecision::cpp_rational>();  
      |    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~  
./boost/multiprecision/detail/generic_interconvert.hpp:375:18: error: call of overloaded 'ldexp(__float128&, int)' is ambiguous  
  375 |    result = ldexp(result, static_cast<int>(-shift));  
      |             ~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from libs/multiprecision/test/test.hpp:11:  
/usr/include/c++/14.2/cmath:311:3: note: candidate: 'constexpr long double std::ldexp(long double, int)'  
  311 |   ldexp(long double __x, int __exp)  
      |   ^~~~~  
/usr/include/c++/14.2/cmath:307:3: note: candidate: 'constexpr float std::ldexp(float, int)'  
  307 |   ldexp(float __x, int __exp)  
      |   ^~~~~  
In file included from /usr/include/features.h:524,  
                 from /usr/include/c++/14.2/x86_64-unknown-linux-gnu/bits/os_defines.h:39,  
                 from /usr/include/c++/14.2/x86_64-unknown-linux-gnu/bits/c++config.h:683,  
                 from /usr/include/c++/14.2/functional:48,  
                 from libs/multiprecision/test/test_arithmetic.hpp:10:  
/usr/include/bits/mathcalls.h:119:1: note: candidate: 'double ldexp(double, int)'  
  119 | __MATHCALL (ldexp,, (_Mdouble_ __x, int __exponent));  
      | ^~~~~~~~~~  
  
    "g++"   -fvisibility-inlines-hidden -fstack-clash-protection -D_FORTIFY_SOURCE=2 -mtune=generic -O2 -pipe    -ffile-prefix-map=/builddir/boost-1.83.0=. -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -Wall -Wextra -fstack-clash-protection -D_FORTIFY_SOURCE=2 -mtune=generic -O2 -pipe    -ffile-prefix-map=/builddir/boost-1.83.0=. -DBOOST_ALL_NO_LIB=1 -DNDEBUG   -I"." -I"libs/multiprecision/include"  -c -o "bin.v2/libs/multiprecision/test/standalone_test_arithmetic_cpp_rational.test/gcc-14/release/threading-multi/visibility-hidden/standalone_test_arithmetic_cpp_rational.o" "libs/multiprecision/test/standalone_test_arithmetic_cpp_rational.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/multiprecision/test/standalone_test_arithmetic_cpp_rational.test/gcc-14/release/threading-multi/visibility-hidden/standalone_test_arithmetic_cpp_rational.o...  
...skipped <pbin.v2/libs/multiprecision/test/standalone_test_arithmetic_cpp_rational.test/gcc-14/release/threading-multi/visibility-hidden>standalone_test_arithmetic_cpp_rational for lack of <pbin.v2/libs/multiprecision/test/standalone_test_arithmetic_cpp_rational.test/gcc-14/release/threading-multi/visibility-hidden>standalone_test_arithmetic_cpp_rational.o...  
...skipped <pbin.v2/libs/multiprecision/test/standalone_test_arithmetic_cpp_rational.test/gcc-14/release/threading-multi/visibility-hidden>standalone_test_arithmetic_cpp_rational.run for lack of <pbin.v2/libs/multiprecision/test/standalone_test_arithmetic_cpp_rational.test/gcc-14/release/threading-multi/visibility-hidden>standalone_test_arithmetic_cpp_rational...  
...failed updating 1 target...  
...skipped 3 targets...  
=> ERROR: do_check: boost::multiprecision: failure  
```  
  
</details>

---

## Comment 1

> Username: jzmaddock  
> Created at: 2025-06-26 17:51:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/697#issuecomment-3009316696  

Confirmed, reproducer is:  
  
```  
#include <boost/multiprecision/cpp_int.hpp>  
#include <iostream>  
  
int main()  
{  
   boost::multiprecision::cpp_rational rat(1, 2);  
   __float128 f = rat.convert_to<__float128>();  
}  
  
```  
  
But only when built with `-DBOOST_MP_STANDALONE`.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2026-02-06 11:22:48 UTC  
> Updated at: 2026-02-06 11:23:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/697#issuecomment-3859864677  

Hi John (@jzmaddock) and @JkktBkkt,  
  
I was looking into this and the problem seems to be gone in 1.90.  
  
I can compile the following program with zero errors and zero warnings locally on GCC11.  
  
```  
#include <boost/multiprecision/cpp_int.hpp>  
  
auto main() -> int  
{  
   boost::multiprecision::cpp_rational rat(1, 2);  
   __float128 f = rat.convert_to<__float128>();  
  
   static_cast<void>(f);  
}  
```  
  
The compile command is:  
  
```bash  
 g++ -O2 -Wall -Wextra -Wpedantic -Wconversion -Wsign-conversion -DBOOST_MP_STANDALONE -I/mnt/c/boost/boost_1_90_0 test.cpp -lquadmath -o test  
```

---

## Comment 3

> Username: jzmaddock  
> Created at: 2026-02-08 16:43:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/697#issuecomment-3867527435  

I can't reproduce any more either, so it looks to be gone?

---

## Comment 4

> Username: ckormanyos  
> Created at: 2026-02-09 20:18:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/697#issuecomment-3873628888  

Closed due to non-reproducability
