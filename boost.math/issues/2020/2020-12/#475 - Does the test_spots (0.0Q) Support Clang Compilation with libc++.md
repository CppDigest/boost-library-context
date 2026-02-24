# #475 - Does the test_spots (0.0Q) Support Clang Compilation with libc++? [Closed]

> Username: linjx123456  
> Created at: 2020-12-28 13:06:16 UTC  
> Updated at: 2020-12-28 16:38:33 UTC  
> Closed at: 2020-12-28 13:31:42 UTC  
> Url: https://github.com/boostorg/math/issues/475  

../boost/lexical_cast/detail/converter_lexical.hpp:243:13: error: static_assert failed due to requirement 'has_right_shift<std::__1::basic_istream<char>, __float128, boost::detail::has_right_shift_impl::dont_care>::value || boost::has_right_shift<std::__1::basic_istream<wchar_t>, __float128, boost::detail::has_right_shift_impl::dont_care>::value' "Target type is neither std::istream`able nor std::wistream`able"  
  
BOOST_AUTO_TEST_CASE( test_main )  
{  
BOOST_MATH_CONTROL_FP;  
test_spots(0.0Q); -----------------The error is caused by test_spots(0.0Q); in test_factorials.cpp  
cout << "max factorial for __float128" << boost::math::max_factorialboost::floatmax_t::value << endl;  
}  
  
compiling Using the LLVM ,However, the GCC compilation is normal.  
Another issue is test_spots (0.0Q); supported? test_spots (0.0F); is ok

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-12-28 16:38:33 UTC  
> Url: https://github.com/boostorg/math/issues/475#issuecomment-751779069  

__float128 is NOT supported with clang, and so neither is that test.  
  
I see no such line in test_factorials.cpp BTW.
