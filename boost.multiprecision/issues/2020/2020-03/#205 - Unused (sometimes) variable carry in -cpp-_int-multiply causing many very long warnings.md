# #205 - Unused (sometimes) variable carry in /cpp/_int/multiply causing many very long warnings. [Closed]

> Username: pabristow  
> Created at: 2020-03-30 11:38:54 UTC  
> Updated at: 2020-04-01 17:18:14 UTC  
> Closed at: 2020-04-01 17:18:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/205  

Compiling math tests using GCC 10.0.1 on mingw-64 develop   
8aa6ab1323e4ef4983796e83484e1d43d70bd9b1  
  
boost/multiprecision/cpp_int/multiply.hpp  
  
   double_limb_type carry = 0;  
  
is repeatedly reported as unused by math tests and examples causing a lot of noise in log files.  
  
Would it be useful to either reposition the definition in the maze of macro tests, or more simply just suppress the warning?  
  
```In file included from ..\..\../boost/multiprecision/cpp_int.hpp:2248,  
                 from ..\..\../boost/multiprecision/cpp_bin_float.hpp:9,  
                 from ..\example\distribution_construction.cpp:52:  
..\..\../boost/multiprecision/cpp_int/multiply.hpp: In instantiation of 'constexpr typename boost::enable_if_c<(((! boost::multiprecision::backends::is_trivial_cpp_int<boost::multiprecision::backends::cpp_int_backend<MinBits, MaxBits, SignType, Checked, Allocator> >::value) && (! boost::multiprecision::backends::is_trivial_cpp_int<boost::multiprecision::backends::cpp_int_backend<MinBits2, MaxBits2, SignType2, Checked2, Allocator2> >::value)) && (! boost::multiprecision::backends::is_trivial_cpp_int<boost::multiprecision::backends::cpp_int_backend<MinBits3, MaxBits3, SignType3, Checked3, Allocator3> >::value))>::type boost::multiprecision::backends::eval_multiply(boost::multiprecision::backends::cpp_int_backend<MinBits, MaxBits, SignType, Checked, Allocator>&, const boost::multiprecision::backends::cpp_int_backend<MinBits2, MaxBits2, SignType2, Checked2, Allocator2>&, const boost::multiprecision::backends::cpp_int_backend<MinBits3, MaxBits3, SignType3, Checked3, Allocator3>&) [with unsigned int MinBits1 = 0; unsigned int MaxBits1 = 0; boost::multiprecision::cpp_integer_type SignType1 = boost::multiprecision::signed_magnitude; boost::multiprecision::cpp_int_check_type Checked1 = boost::multiprecision::unchecked; Allocator1 = std::allocator<long long unsigned int>; unsigned int MinBits2 = 0; unsigned int MaxBits2 = 0; boost::multiprecision::cpp_integer_type SignType2 = boost::multiprecision::signed_magnitude; boost::multiprecision::cpp_int_check_type Checked2 = boost::multiprecision::unchecked; Allocator2 = std::allocator<long long unsigned int>; unsigned int MinBits3 = 0; unsigned int MaxBits3 = 0; boost::multiprecision::cpp_integer_type SignType3 = boost::multiprecision::signed_magnitude; boost::multiprecision::cpp_int_check_type Checked3 = boost::multiprecision::unchecked; Allocator3 = std::allocator<long long unsigned int>; typename boost::enable_if_c<(((! boost::multiprecision::backends::is_trivial_cpp_int<boost::multiprecision::backends::cpp_int_backend<MinBits, MaxBits, SignType, Checked, Allocator> >::value) && (! boost::multiprecision::backends::is_trivial_cpp_int<boost::multiprecision::backends::cpp_int_backend<MinBits2, MaxBits2, SignType2, Checked2, Allocator2> >::value)) && (! boost::multiprecision::backends::is_trivial_cpp_int<boost::multiprecision::backends::cpp_int_backend<MinBits3, MaxBits3, SignType3, Checked3, Allocator3> >::value))>::type = void]':  
..\..\../boost/multiprecision/number.hpp:1019:20:   required from 'constexpr void boost::multiprecision::number<Backend, ExpressionTemplates>::do_assign(const Exp&, const boost::multiprecision::detail::multiply_immediates&) [with Exp = boost::multiprecision::detail::expression<boost::multiprecision::detail::multiply_immediates, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, void, void>; Backend = boost::multiprecision::backends::cpp_int_backend<>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_on]'  
..\..\../boost/multiprecision/number.hpp:988:16:   required from 'constexpr void boost::multiprecision::number<Backend, ExpressionTemplates>::do_assign(const boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>&, const true_&) [with tag = boost::multiprecision::detail::multiply_immediates; Arg1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >; Arg2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >; Arg3 = void; Arg4 = void; Backend = boost::multiprecision::backends::cpp_int_backend<>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_on; mpl_::true_ = mpl_::bool_<true>]'  
..\..\../boost/multiprecision/number.hpp:212:16:   required from 'constexpr typename boost::enable_if_c<boost::is_convertible<typename boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type, boost::multiprecision::number<Backend, ExpressionTemplates> >::value, boost::multiprecision::number<Backend, ExpressionTemplates>&>::type boost::multiprecision::number<Backend, ExpressionTemplates>::operator=(const boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>&) [with tag = boost::multiprecision::detail::multiply_immediates; Arg1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >; Arg2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >; Arg3 = void; Arg4 = void; Backend = boost::multiprecision::backends::cpp_int_backend<>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_on; typename boost::enable_if_c<boost::is_convertible<typename boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type, boost::multiprecision::number<Backend, ExpressionTemplates> >::value, boost::multiprecision::number<Backend, ExpressionTemplates>&>::type = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >&]'  
..\..\../boost/multiprecision/cpp_bin_float/io.hpp:24:4:   required from 'I boost::multiprecision::cpp_bf_io_detail::restricted_multiply(boost::multiprecision::cpp_int&, const cpp_int&, const cpp_int&, I, int64_t&) [with I = long long int; boost::multiprecision::cpp_int = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >; int64_t = long long int]'  
..\..\../boost/multiprecision/cpp_bin_float/io.hpp:306:85:   required from 'boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>& boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::operator=(const char*) [with unsigned int Digits = 50; boost::multiprecision::backends::digit_base_type DigitBase = boost::multiprecision::backends::digit_base_10; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0]'  
..\..\../boost/multiprecision/number.hpp:91:17:   required from 'constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const V&, typename boost::enable_if_c<((((boost::is_arithmetic<V>::value || boost::is_same<std::__cxx11::basic_string<char>, V>::value) || boost::is_convertible<V, const char*>::value) && (! boost::multiprecision::detail::is_explicitly_convertible<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value)) && boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value)>::type*) [with V = char [32]; Backend = boost::multiprecision::backends::cpp_bin_float<50>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off; typename boost::enable_if_c<((((boost::is_arithmetic<V>::value || boost::is_same<std::__cxx11::basic_string<char>, V>::value) || boost::is_convertible<V, const char*>::value) && (! boost::multiprecision::detail::is_explicitly_convertible<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value)) && boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value)>::type = void]'  
..\example\distribution_construction.cpp:203:55:   required from here  
..\..\../boost/multiprecision/cpp_int/multiply.hpp:453:21: warning: unused variable 'carry' [-Wunused-variable]  
  453 |    double_limb_type carry = 0;  
      |                     ^~~~~  
```

---

## Comment 1

> Username: madhur4127  
> Created at: 2020-03-30 12:19:19 UTC  
> Updated at: 2020-03-30 12:19:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/205#issuecomment-605965568  

Thanks for notifying it, Paul. #206 fixes this. This feature is supposed to get activated based on GCC 10.  
  
@pabristow, are there any unusual test failures that might be because of this feature? It would be really helpful if you could define `BOOST_MP_COMBA` macro and report anything that's unusual like this.  
  
I have disabled auto enabling of Comba multiplier (for GCC 10) because I didn't see those warnings when I tested it on GCC 10 experimental. Once GCC 10 is released, I will test for more warnings and performance.
