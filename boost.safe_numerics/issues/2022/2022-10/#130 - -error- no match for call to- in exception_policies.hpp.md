# #130 - "error: no match for call to" in exception_policies.hpp [Open]

> Username: adambadura  
> Created at: 2022-10-10 09:16:31 UTC  
> Updated at: 2022-10-10 09:21:46 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/130  

While working on a sample for #129 I tried the following code ([godbolt.org](https://godbolt.org/z/qaW55EKj1)):  
```c++  
using delta_safe_t = boost::safe_numerics::safe_signed_range<  
  -1,  
  1,  
  boost::safe_numerics::native,  
  boost::safe_numerics::loose_trap_policy  
>;  
  
template<int I>  
using const_safe_t = boost::safe_numerics::safe_signed_literal<  
  I,  
  boost::safe_numerics::native,  
  boost::safe_numerics::loose_trap_policy  
>;  
  
int main() {  
  constexpr auto x = const_safe_t<8>{};  
  std::cout << "x = " << safe_format(x) << '\n';  
  constexpr auto y = delta_safe_t{1};  
  std::cout << "y = " << safe_format(y) << '\n';  
  constexpr auto z = y * x;  
  std::cout << "z = " << safe_format(z) << '\n';  
}  
```  
but it keeps failing with a lengthy error:  
```plain  
In file included from /opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_base.hpp:17,  
                 from /opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_integer.hpp:15,  
                 from /opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_integer_range.hpp:13,  
                 from <source>:3:  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/exception_policies.hpp: In instantiation of 'static constexpr void boost::safe_numerics::exception_policy<AE, IDB, UB, UV>::on_arithmetic_error(const boost::safe_numerics::safe_numerics_error&, const char*) [with AE = boost::safe_numerics::trap_exception; IDB = boost::safe_numerics::ignore_exception; UB = boost::safe_numerics::ignore_exception; UV = boost::safe_numerics::ignore_exception]':  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_base_operations.hpp:60:36:   required from 'static constexpr boost::safe_numerics::checked_result<R> boost::safe_numerics::heterogeneous_checked_operation<R, Min, Max, T, F, typename std::enable_if<(std::is_integral<_Tp>::value && std::is_integral<T>::value)>::type>::cast_impl_detail::cast_impl(const T&, std::true_type, std::true_type) [with R = signed char; R Min = -1; R Max = 1; T = int; F = boost::safe_numerics::dispatch_and_return<boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception>, signed char>; std::true_type = std::integral_constant<bool, true>]'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/checked_integer.hpp:148:40:   required from here  
<source>:71:36:   in 'constexpr' expansion of 'boost::safe_numerics::safe_base<signed char, -1, 1, boost::safe_numerics::native, boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception> >(1)'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_base_operations.hpp:195:23:   in 'constexpr' expansion of '((boost::safe_numerics::safe_base<signed char, -1, 1, boost::safe_numerics::native, boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception> >*)this)->boost::safe_numerics::safe_base<signed char, -1, 1, boost::safe_numerics::native, boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception> >::validated_cast<int>((* & t))'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_base_operations.hpp:154:59:   in 'constexpr' expansion of 'boost::safe_numerics::validate_detail<signed char, -1, 1, boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception> >::return_value<int>((* & t))'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_base_operations.hpp:146:30:   in 'constexpr' expansion of 'boost::safe_numerics::validate_detail<signed char, -1, 1, boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception> >::exception_possible::return_value<int>((* & t))'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_base_operations.hpp:116:20:   in 'constexpr' expansion of 'boost::safe_numerics::heterogeneous_checked_operation<signed char, -1, 1, int, boost::safe_numerics::dispatch_and_return<boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception>, signed char>, void>::cast((* & t))'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/checked_integer.hpp:68:81:   in 'constexpr' expansion of 'boost::safe_numerics::dispatch_and_return<boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception>, signed char>::invoke<boost::safe_numerics::safe_numerics_error::positive_overflow_error>(((const char*)"converted signed value too large"))'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_base_operations.hpp:92:24:   in 'constexpr' expansion of 'boost::safe_numerics::dispatch<exception_policy<trap_exception, ignore_exception, ignore_exception, ignore_exception>, boost::safe_numerics::safe_numerics_error::positive_overflow_error>(((const char*)msg))'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_base_operations.hpp:82:50:   in 'constexpr' expansion of 'boost::safe_numerics::dispatch_switch::dispatch_case<boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception>, boost::safe_numerics::safe_numerics_actions::arithmetic_error>::invoke(boost::safe_numerics::safe_numerics_error::positive_overflow_error, msg)'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/exception_policies.hpp:28:13: error: no match for call to '(boost::safe_numerics::trap_exception) (const boost::safe_numerics::safe_numerics_error&, const char*&)'  
   28 |         AE()(e, msg);  
      |         ~~~~^~~~~~~~  
ASM generation compiler returned: 1  
In file included from /opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_base.hpp:17,  
                 from /opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_integer.hpp:15,  
                 from /opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_integer_range.hpp:13,  
                 from <source>:3:  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/exception_policies.hpp: In instantiation of 'static constexpr void boost::safe_numerics::exception_policy<AE, IDB, UB, UV>::on_arithmetic_error(const boost::safe_numerics::safe_numerics_error&, const char*) [with AE = boost::safe_numerics::trap_exception; IDB = boost::safe_numerics::ignore_exception; UB = boost::safe_numerics::ignore_exception; UV = boost::safe_numerics::ignore_exception]':  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_base_operations.hpp:60:36:   required from 'static constexpr boost::safe_numerics::checked_result<R> boost::safe_numerics::heterogeneous_checked_operation<R, Min, Max, T, F, typename std::enable_if<(std::is_integral<_Tp>::value && std::is_integral<T>::value)>::type>::cast_impl_detail::cast_impl(const T&, std::true_type, std::true_type) [with R = signed char; R Min = -1; R Max = 1; T = int; F = boost::safe_numerics::dispatch_and_return<boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception>, signed char>; std::true_type = std::integral_constant<bool, true>]'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/checked_integer.hpp:148:40:   required from here  
<source>:71:36:   in 'constexpr' expansion of 'boost::safe_numerics::safe_base<signed char, -1, 1, boost::safe_numerics::native, boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception> >(1)'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_base_operations.hpp:195:23:   in 'constexpr' expansion of '((boost::safe_numerics::safe_base<signed char, -1, 1, boost::safe_numerics::native, boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception> >*)this)->boost::safe_numerics::safe_base<signed char, -1, 1, boost::safe_numerics::native, boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception> >::validated_cast<int>((* & t))'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_base_operations.hpp:154:59:   in 'constexpr' expansion of 'boost::safe_numerics::validate_detail<signed char, -1, 1, boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception> >::return_value<int>((* & t))'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_base_operations.hpp:146:30:   in 'constexpr' expansion of 'boost::safe_numerics::validate_detail<signed char, -1, 1, boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception> >::exception_possible::return_value<int>((* & t))'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_base_operations.hpp:116:20:   in 'constexpr' expansion of 'boost::safe_numerics::heterogeneous_checked_operation<signed char, -1, 1, int, boost::safe_numerics::dispatch_and_return<boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception>, signed char>, void>::cast((* & t))'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/checked_integer.hpp:68:81:   in 'constexpr' expansion of 'boost::safe_numerics::dispatch_and_return<boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception>, signed char>::invoke<boost::safe_numerics::safe_numerics_error::positive_overflow_error>(((const char*)"converted signed value too large"))'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_base_operations.hpp:92:24:   in 'constexpr' expansion of 'boost::safe_numerics::dispatch<exception_policy<trap_exception, ignore_exception, ignore_exception, ignore_exception>, boost::safe_numerics::safe_numerics_error::positive_overflow_error>(((const char*)msg))'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/safe_base_operations.hpp:82:50:   in 'constexpr' expansion of 'boost::safe_numerics::dispatch_switch::dispatch_case<boost::safe_numerics::exception_policy<boost::safe_numerics::trap_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception, boost::safe_numerics::ignore_exception>, boost::safe_numerics::safe_numerics_actions::arithmetic_error>::invoke(boost::safe_numerics::safe_numerics_error::positive_overflow_error, msg)'  
/opt/compiler-explorer/libs/boost_1_80_0/boost/safe_numerics/exception_policies.hpp:28:13: error: no match for call to '(boost::safe_numerics::trap_exception) (const boost::safe_numerics::safe_numerics_error&, const char*&)'  
   28 |         AE()(e, msg);  
      |         ~~~~^~~~~~~~  
```  
  
What is wrong with my code?
