# #324 - Enum class conversion broken since 1.76.0 [Closed]

> Username: wak-google  
> Created at: 2021-05-16 01:04:06 UTC  
> Updated at: 2021-06-06 07:20:39 UTC  
> Closed at: 2021-06-06 07:20:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/324  

Consider the following code snippet (`g++ -std=c++17 -o main main.cpp`)  
```  
#include <boost/multiprecision/number.hpp>  
#include <boost/multiprecision/cpp_int.hpp>  
  
template <unsigned N>  
using fixed_uint_t =  
    boost::multiprecision::number<boost::multiprecision::cpp_int_backend<  
        N, N, boost::multiprecision::unsigned_magnitude,  
        boost::multiprecision::unchecked, void>>;  
  
enum class Status : uint8_t  
{  
    ok = 0,  
    unknown = 1,  
};  
  
int main()  
{  
    auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
    return 0;  
}  
```  
  
This used to compile with 1.75.0, but now produces  
```  
main.cpp: In function ‘int main()’:  
main.cpp:18:53: error: no matching function for call to ‘boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>::number(Status)’  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:317:50: note: candidate: ‘template<class Other, boost::multiprecision::expression_template_option ET> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(boost::multiprecision::number<Other, ET>&&, typename std::enable_if<(std::is_convertible<Other, Backend>::value && (! boost::multiprecision::detail::is_restricted_conversion<Other, Backend>::value))>::type*) [with Other = Other; boost::multiprecision::expression_template_option ET = ET; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
  317 |    BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR number(number<Other, ET>&& val,  
      |                                                  ^~~~~~  
include/boost/multiprecision/number.hpp:317:50: note:   template argument deduction/substitution failed:  
main.cpp:18:53: note:   mismatched types ‘boost::multiprecision::number<Backend, ExpressionTemplates>’ and ‘Status’  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:307:35: note: candidate: ‘constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(boost::multiprecision::number<Backend, ExpressionTemplates>&&) [with Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
  307 |    BOOST_MP_FORCEINLINE constexpr number(number&& r)  
      |                                   ^~~~~~  
include/boost/multiprecision/number.hpp:307:51: note:   no known conversion for argument 1 from ‘Status’ to ‘boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>&&’  
  307 |    BOOST_MP_FORCEINLINE constexpr number(number&& r)  
      |                                          ~~~~~~~~~^  
include/boost/multiprecision/number.hpp:297:38: note: candidate: ‘template<class tag, class Arg1, class Arg2, class Arg3, class Arg4> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>&, typename std::enable_if<((! std::is_convertible<typename boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type, boost::multiprecision::number<Backend, ExpressionTemplates> >::value) && boost::multiprecision::detail::is_explicitly_convertible<typename boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type, boost::multiprecision::number<Backend, ExpressionTemplates> >::value)>::type*) [with tag = tag; Arg1 = Arg1; Arg2 = Arg2; Arg3 = Arg3; Arg4 = Arg4; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
  297 |    explicit BOOST_MP_CXX14_CONSTEXPR number(const detail::expression<tag, Arg1, Arg2, Arg3, Arg4>& e,  
      |                                      ^~~~~~  
include/boost/multiprecision/number.hpp:297:38: note:   template argument deduction/substitution failed:  
main.cpp:18:53: note:   mismatched types ‘const boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>’ and ‘Status’  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:289:29: note: candidate: ‘template<class tag, class Arg1, class Arg2, class Arg3, class Arg4> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>&, typename std::enable_if<std::is_convertible<typename boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type, boost::multiprecision::number<Backend, ExpressionTemplates> >::value>::type*) [with tag = tag; Arg1 = Arg1; Arg2 = Arg2; Arg3 = Arg3; Arg4 = Arg4; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
  289 |    BOOST_MP_CXX14_CONSTEXPR number(const detail::expression<tag, Arg1, Arg2, Arg3, Arg4>& e, typename std::enable_if<std::is_convertible<typename detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type, self_type>::value>::type* = 0)  
      |                             ^~~~~~  
include/boost/multiprecision/number.hpp:289:29: note:   template argument deduction/substitution failed:  
main.cpp:18:53: note:   mismatched types ‘const boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>’ and ‘Status’  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:175:50: note: candidate: ‘template<class Other, boost::multiprecision::expression_template_option ET> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const boost::multiprecision::number<Other, ET>&, const boost::multiprecision::number<Other, ET>&, typename std::enable_if<std::is_convertible<Other, Backend>::value>::type*) [with Other = Other; boost::multiprecision::expression_template_option ET = ET; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
  175 |    BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR number(const number<Other, ET>& v1, const number<Other, ET>& v2, typename std::enable_if<std::is_convertible<Other, Backend>::value>::type* = 0)  
      |                                                  ^~~~~~  
include/boost/multiprecision/number.hpp:175:50: note:   template argument deduction/substitution failed:  
main.cpp:18:53: note:   mismatched types ‘const boost::multiprecision::number<Backend, ExpressionTemplates>’ and ‘Status’  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:170:59: note: candidate: ‘template<class V, class U> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const V&, const U&, unsigned int, typename std::enable_if<((((std::is_constructible<typename boost::multiprecision::component_type<boost::multiprecision::number<Backend, ExpressionTemplates> >::type, V>::value || std::is_convertible<V, std::__cxx11::basic_string<char> >::value) && (std::is_constructible<typename boost::multiprecision::component_type<boost::multiprecision::number<Backend, ExpressionTemplates> >::type, U>::value || std::is_convertible<U, std::__cxx11::basic_string<char> >::value)) && (! std::is_same<typename boost::multiprecision::component_type<boost::multiprecision::number<Backend, ExpressionTemplates> >::type, boost::multiprecision::number<Backend, ExpressionTemplates> >::value)) && (!(boost::is_convertible<V, typename boost::multiprecision::component_type<boost::multiprecision::number<Backend, ExpressionTemplates> >::type>::value && boost::is_convertible<U, typename boost::multiprecision::component_type<boost::multiprecision::number<Backend, ExpressionTemplates> >::type>::value)))>::type*) [with V = V; U = U; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
  170 |    BOOST_MP_FORCEINLINE explicit BOOST_MP_CXX14_CONSTEXPR number(const V& v1, const U& v2, unsigned digits10,  
      |                                                           ^~~~~~  
include/boost/multiprecision/number.hpp:170:59: note:   template argument deduction/substitution failed:  
main.cpp:18:53: note:   candidate expects 4 arguments, 1 provided  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:165:50: note: candidate: ‘template<class V, class U> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const V&, const U&, unsigned int, typename std::enable_if<((std::is_convertible<V, typename boost::multiprecision::component_type<boost::multiprecision::number<Backend, ExpressionTemplates> >::type>::value && std::is_convertible<U, typename boost::multiprecision::component_type<boost::multiprecision::number<Backend, ExpressionTemplates> >::type>::value) && (! std::is_same<typename boost::multiprecision::component_type<boost::multiprecision::number<Backend, ExpressionTemplates> >::type, boost::multiprecision::number<Backend, ExpressionTemplates> >::value))>::type*) [with V = V; U = U; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
  165 |    BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR number(const V& v1, const U& v2, unsigned digits10,  
      |                                                  ^~~~~~  
include/boost/multiprecision/number.hpp:165:50: note:   template argument deduction/substitution failed:  
main.cpp:18:53: note:   candidate expects 4 arguments, 1 provided  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:153:59: note: candidate: ‘template<class Traits> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const std::basic_string_view<char, Traits>&, unsigned int) [with Traits = Traits; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
  153 |    explicit BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR number(const std::basic_string_view<char, Traits>& v, unsigned digits10)  
      |                                                           ^~~~~~  
include/boost/multiprecision/number.hpp:153:59: note:   template argument deduction/substitution failed:  
main.cpp:18:53: note:   mismatched types ‘const std::basic_string_view<char, Traits>’ and ‘Status’  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:147:45: note: candidate: ‘template<class Traits> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const std::basic_string_view<char, Traits>&, const std::basic_string_view<char, Traits>&) [with Traits = Traits; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
  147 |    explicit inline BOOST_MP_CXX14_CONSTEXPR number(const std::basic_string_view<char, Traits>& view_x, const std::basic_string_view<char, Traits>& view_y)  
      |                                             ^~~~~~  
include/boost/multiprecision/number.hpp:147:45: note:   template argument deduction/substitution failed:  
main.cpp:18:53: note:   mismatched types ‘const std::basic_string_view<char, Traits>’ and ‘Status’  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:141:45: note: candidate: ‘template<class Traits> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const std::basic_string_view<char, Traits>&) [with Traits = Traits; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
  141 |    explicit inline BOOST_MP_CXX14_CONSTEXPR number(const std::basic_string_view<char, Traits>& view)  
      |                                             ^~~~~~  
include/boost/multiprecision/number.hpp:141:45: note:   template argument deduction/substitution failed:  
main.cpp:18:53: note:   mismatched types ‘const std::basic_string_view<char, Traits>’ and ‘Status’  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:128:59: note: candidate: ‘template<class V, class U> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const V&, const U&, typename std::enable_if<(((((std::is_constructible<typename boost::multiprecision::component_type<boost::multiprecision::number<Backend, ExpressionTemplates> >::type, V>::value || std::is_convertible<V, std::__cxx11::basic_string<char> >::value) && (std::is_constructible<typename boost::multiprecision::component_type<boost::multiprecision::number<Backend, ExpressionTemplates> >::type, U>::value || std::is_convertible<U, std::__cxx11::basic_string<char> >::value)) && (! std::is_same<typename boost::multiprecision::component_type<boost::multiprecision::number<Backend, ExpressionTemplates> >::type, boost::multiprecision::number<Backend, ExpressionTemplates> >::value)) && (! std::is_same<V, boost::multiprecision::number<Backend, ExpressionTemplates> >::value)) && (!(std::is_convertible<V, typename boost::multiprecision::component_type<boost::multiprecision::number<Backend, ExpressionTemplates> >::type>::value && std::is_convertible<U, typename boost::multiprecision::component_type<boost::multiprecision::number<Backend, ExpressionTemplates> >::type>::value)))>::type*) [with V = V; U = U; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
  128 |    BOOST_MP_FORCEINLINE explicit BOOST_MP_CXX14_CONSTEXPR number(const V& v1, const U& v2,  
      |                                                           ^~~~~~  
include/boost/multiprecision/number.hpp:128:59: note:   template argument deduction/substitution failed:  
main.cpp:18:53: note:   candidate expects 3 arguments, 1 provided  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:120:50: note: candidate: ‘template<class V, class U> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const V&, const U&, typename std::enable_if<((std::is_convertible<V, typename boost::multiprecision::component_type<boost::multiprecision::number<Backend, ExpressionTemplates> >::type>::value && std::is_convertible<U, typename boost::multiprecision::component_type<boost::multiprecision::number<Backend, ExpressionTemplates> >::type>::value) && (! std::is_same<typename boost::multiprecision::component_type<boost::multiprecision::number<Backend, ExpressionTemplates> >::type, boost::multiprecision::number<Backend, ExpressionTemplates> >::value))>::type*) [with V = V; U = U; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
  120 |    BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR number(const V& v1, const U& v2,  
      |                                                  ^~~~~~  
include/boost/multiprecision/number.hpp:120:50: note:   template argument deduction/substitution failed:  
main.cpp:18:53: note:   candidate expects 3 arguments, 1 provided  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:115:59: note: candidate: ‘template<class Other, boost::multiprecision::expression_template_option ET> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const boost::multiprecision::number<Other, ET>&, typename std::enable_if<(boost::multiprecision::detail::is_explicitly_convertible<Other, Backend>::value && (boost::multiprecision::detail::is_restricted_conversion<Other, Backend>::value || (! std::is_convertible<Other, Backend>::value)))>::type*) [with Other = Other; boost::multiprecision::expression_template_option ET = ET; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
  115 |    explicit BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR number(const number<Other, ET>& val, typename std::enable_if<  
      |                                                           ^~~~~~  
include/boost/multiprecision/number.hpp:115:59: note:   template argument deduction/substitution failed:  
main.cpp:18:53: note:   mismatched types ‘const boost::multiprecision::number<Backend, ExpressionTemplates>’ and ‘Status’  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:103:38: note: candidate: ‘template<class Other, boost::multiprecision::expression_template_option ET> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const boost::multiprecision::number<Other, ET>&, typename std::enable_if<(! boost::multiprecision::detail::is_explicitly_convertible<Other, Backend>::value)>::type*) [with Other = Other; boost::multiprecision::expression_template_option ET = ET; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
  103 |    explicit BOOST_MP_CXX14_CONSTEXPR number(const number<Other, ET>& val, typename std::enable_if<  
      |                                      ^~~~~~  
include/boost/multiprecision/number.hpp:103:38: note:   template argument deduction/substitution failed:  
main.cpp:18:53: note:   mismatched types ‘const boost::multiprecision::number<Backend, ExpressionTemplates>’ and ‘Status’  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:97:50: note: candidate: ‘template<class Other, boost::multiprecision::expression_template_option ET> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const boost::multiprecision::number<Other, ET>&, typename std::enable_if<(std::is_convertible<Other, Backend>::value && (! boost::multiprecision::detail::is_restricted_conversion<Other, Backend>::value))>::type*) [with Other = Other; boost::multiprecision::expression_template_option ET = ET; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
   97 |    BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR number(const number<Other, ET>& val,  
      |                                                  ^~~~~~  
include/boost/multiprecision/number.hpp:97:50: note:   template argument deduction/substitution failed:  
main.cpp:18:53: note:   mismatched types ‘const boost::multiprecision::number<Backend, ExpressionTemplates>’ and ‘Status’  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:93:35: note: candidate: ‘template<boost::multiprecision::expression_template_option ET> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const boost::multiprecision::number<Backend, ET>&) [with boost::multiprecision::expression_template_option ET = ET; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
   93 |    BOOST_MP_FORCEINLINE constexpr number(const number<Backend, ET>& val)  
      |                                   ^~~~~~  
include/boost/multiprecision/number.hpp:93:35: note:   template argument deduction/substitution failed:  
main.cpp:18:53: note:   mismatched types ‘const boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, ET>’ and ‘Status’  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:89:44: note: candidate: ‘template<class V> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const V&, unsigned int, typename std::enable_if<(((((boost::multiprecision::detail::is_arithmetic<V>::value || std::is_same<std::__cxx11::basic_string<char>, V>::value) || std::is_convertible<V, const char*>::value) && boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value) && (boost::multiprecision::number_category<Backend>::value != boost::multiprecision::number_kind_complex)) && (boost::multiprecision::number_category<Backend>::value != boost::multiprecision::number_kind_rational))>::type*) [with V = V; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
   89 |    explicit BOOST_MP_FORCEINLINE constexpr number(const V& v, unsigned digits10, typename std::enable_if<(boost::multiprecision::detail::is_arithmetic<V>::value || std::is_same<std::string, V>::value || std::is_convertible<V, const char*>::value) && detail::is_restricted_conversion<typename detail::canonical<V, Backend>::type, Backend>::value && (boost::multiprecision::number_category<Backend>::value != boost::multiprecision::number_kind_complex) && (boost::multiprecision::number_category<Backend>::value != boost::multiprecision::number_kind_rational)>::type* = 0)  
      |                                            ^~~~~~  
include/boost/multiprecision/number.hpp:89:44: note:   template argument deduction/substitution failed:  
main.cpp:18:53: note:   candidate expects 3 arguments, 1 provided  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:84:44: note: candidate: ‘template<class V> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const V&, typename std::enable_if<(boost::multiprecision::detail::is_explicitly_convertible<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value && (boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value || (! std::is_convertible<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value)))>::type*) [with V = V; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
   84 |    explicit BOOST_MP_FORCEINLINE constexpr number(const V& v, typename std::enable_if<  
      |                                            ^~~~~~  
include/boost/multiprecision/number.hpp:84:44: note:   template argument deduction/substitution failed:  
include/boost/multiprecision/number.hpp: In substitution of ‘template<class V> constexpr boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>::number(const V&, typename std::enable_if<(boost::multiprecision::detail::is_explicitly_convertible<typename boost::multiprecision::detail::canonical<V, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::type, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::value && (boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::type, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::value || (! std::is_convertible<typename boost::multiprecision::detail::canonical<V, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::type, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::value))), void>::type*) [with V = Status]’:  
main.cpp:18:53:   required from here  
include/boost/multiprecision/number.hpp:84:44: error: no type named ‘type’ in ‘struct std::enable_if<false, void>’  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:77:59: note: candidate: ‘template<class V> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const V&, typename std::enable_if<((((boost::multiprecision::detail::is_arithmetic<V>::value || std::is_same<std::__cxx11::basic_string<char>, V>::value) || std::is_convertible<V, const char*>::value) && (! boost::multiprecision::detail::is_explicitly_convertible<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value)) && boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value)>::type*) [with V = V; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
   77 |    explicit BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR number(const V& v, typename std::enable_if<  
      |                                                           ^~~~~~  
include/boost/multiprecision/number.hpp:77:59: note:   template argument deduction/substitution failed:  
include/boost/multiprecision/number.hpp: In substitution of ‘template<class V> constexpr boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>::number(const V&, typename std::enable_if<((((boost::multiprecision::detail::is_arithmetic<T>::value || std::is_same<std::__cxx11::basic_string<char>, V>::value) || std::is_convertible<Val, const char*>::value) && (! boost::multiprecision::detail::is_explicitly_convertible<typename boost::multiprecision::detail::canonical<V, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::type, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::value)) && boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::type, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::value), void>::type*) [with V = Status]’:  
main.cpp:18:53:   required from here  
include/boost/multiprecision/number.hpp:77:59: error: no type named ‘type’ in ‘struct std::enable_if<false, void>’  
include/boost/multiprecision/number.hpp:73:35: note: candidate: ‘constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const boost::multiprecision::number<Backend, ExpressionTemplates>&, unsigned int) [with Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
   73 |    BOOST_MP_FORCEINLINE constexpr number(const number& e, unsigned digits10)  
      |                                   ^~~~~~  
include/boost/multiprecision/number.hpp:73:35: note:   candidate expects 2 arguments, 1 provided  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:66:35: note: candidate: ‘template<class V> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const V&, unsigned int, typename std::enable_if<(((((boost::multiprecision::detail::is_arithmetic<V>::value || std::is_same<std::__cxx11::basic_string<char>, V>::value) || std::is_convertible<V, const char*>::value) && (! boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value)) && (boost::multiprecision::number_category<Backend>::value != boost::multiprecision::number_kind_complex)) && (boost::multiprecision::number_category<Backend>::value != boost::multiprecision::number_kind_rational))>::type*) [with V = V; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
   66 |    BOOST_MP_FORCEINLINE constexpr number(const V& v, unsigned digits10, typename std::enable_if<(boost::multiprecision::detail::is_arithmetic<V>::value || std::is_same<std::string, V>::value || std::is_convertible<V, const char*>::value) && !detail::is_restricted_conversion<typename detail::canonical<V, Backend>::type, Backend>::value && (boost::multiprecision::number_category<Backend>::value != boost::multiprecision::number_kind_complex) && (boost::multiprecision::number_category<Backend>::value != boost::multiprecision::number_kind_rational)  
      |                                   ^~~~~~  
include/boost/multiprecision/number.hpp:66:35: note:   template argument deduction/substitution failed:  
main.cpp:18:53: note:   candidate expects 3 arguments, 1 provided  
   18 |     auto b = static_cast<fixed_uint_t<7>>(Status::ok);  
      |                                                     ^  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:58:35: note: candidate: ‘template<class V> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const V&, typename std::enable_if<(std::is_convertible<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value && (! boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value))>::type*) [with V = V; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
   58 |    BOOST_MP_FORCEINLINE constexpr number(const V& v, typename std::enable_if<  
      |                                   ^~~~~~  
include/boost/multiprecision/number.hpp:58:35: note:   template argument deduction/substitution failed:  
include/boost/multiprecision/number.hpp: In substitution of ‘template<class V> constexpr boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>::number(const V&, typename std::enable_if<(std::is_convertible<typename boost::multiprecision::detail::canonical<V, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::type, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::value && (! boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::type, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::value)), void>::type*) [with V = Status]’:  
main.cpp:18:53:   required from here  
include/boost/multiprecision/number.hpp:58:35: error: no type named ‘type’ in ‘struct std::enable_if<false, void>’  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:48:50: note: candidate: ‘template<class V> constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const V&, typename std::enable_if<((((boost::multiprecision::detail::is_arithmetic<V>::value || std::is_same<std::__cxx11::basic_string<char>, V>::value) || std::is_convertible<V, const char*>::value) && (! std::is_convertible<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value)) && (! boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value))>::type*) [with V = V; Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
   48 |    BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR number(const V& v, typename std::enable_if<  
      |                                                  ^~~~~~  
include/boost/multiprecision/number.hpp:48:50: note:   template argument deduction/substitution failed:  
include/boost/multiprecision/number.hpp: In substitution of ‘template<class V> constexpr boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>::number(const V&, typename std::enable_if<((((boost::multiprecision::detail::is_arithmetic<T>::value || std::is_same<std::__cxx11::basic_string<char>, V>::value) || std::is_convertible<Val, const char*>::value) && (! std::is_convertible<typename boost::multiprecision::detail::canonical<V, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::type, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::value)) && (! boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::type, boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> >::value)), void>::type*) [with V = Status]’:  
main.cpp:18:53:   required from here  
include/boost/multiprecision/number.hpp:48:50: error: no type named ‘type’ in ‘struct std::enable_if<false, void>’  
In file included from main.cpp:1:  
include/boost/multiprecision/number.hpp:46:35: note: candidate: ‘constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const boost::multiprecision::number<Backend, ExpressionTemplates>&) [with Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
   46 |    BOOST_MP_FORCEINLINE constexpr number(const number& e) noexcept(noexcept(Backend(std::declval<Backend const&>()))) : m_backend(e.m_backend) {}  
      |                                   ^~~~~~  
include/boost/multiprecision/number.hpp:46:56: note:   no known conversion for argument 1 from ‘Status’ to ‘const boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off>&’  
   46 |    BOOST_MP_FORCEINLINE constexpr number(const number& e) noexcept(noexcept(Backend(std::declval<Backend const&>()))) : m_backend(e.m_backend) {}  
      |                                          ~~~~~~~~~~~~~~^  
include/boost/multiprecision/number.hpp:45:35: note: candidate: ‘constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number() [with Backend = boost::multiprecision::backends::cpp_int_backend<7, 7, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
   45 |    BOOST_MP_FORCEINLINE constexpr number() noexcept(noexcept(Backend())) {}  
      |                                   ^~~~~~  
include/boost/multiprecision/number.hpp:45:35: note:   candidate expects 0 arguments, 1 provided  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2021-05-16 11:43:02 UTC  
> Updated at: 2021-05-16 11:44:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/324#issuecomment-841805749  

> ... used to compile with 1.75.0, but now...  
  
Oh yes. That might be a tricky one. In 1.76, we switched from C++03 to C++11, as the required compiler. In doing so, many class constructors which previously used Boost's compile-time enabling now use `enable_if` via those methods present in `<type_traits>`  
  
I am not entirely sure, but the constructor from enumerated types is probably now disabled because an enumerated type is not `std::is_integral`.  
  
The modified code below with cast of the enumerated instance to `int` compiles.  
  
```  
    auto b = static_cast<fixed_uint_t<7>>((int) Status::ok);  
```  
  
I really do not know which is right, the old 1.75 behavior or the new one in 1.76. I guess we could explicitly enable-if on `is_enum` but @jzmaddock  might have a more standards-oriented reasoning why or why not.

---

## Comment 2

> Username: wak-google  
> Created at: 2021-05-16 23:09:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/324#issuecomment-841891291  

I guess my question is whether or not this is intentional behavior now. I personally don't think it's ergonomic to have `static_cast<fixed_uint_t<7>>(static_cast<uint8_t>(Status::ok))` everywhere.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2021-05-17 05:32:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/324#issuecomment-842000619  

> question is whether or not this is intentional behavior  
  
That is a good question. Personally, I had not thought of it, certainly not really with foresight into that exact example. I believe it is more correct than incorrect since `number<cpp_int_backend>` is intended to behave closely to the behavior of built-in `int`. And, of course, built-in int can not directly be assigned the value from an enumeration class either (see code below).  
  
This does not mean it is absolutely correct or not, as that is quite a subjective term. But the backend `enable_if` expressions are, in fact, quite specific in using `is_arithmetic` and the like to in fact behave as `int`.  
  
Also note that only the argument of construction needs to be casted or converted.  
  
```  
#include <boost/multiprecision/number.hpp>  
#include <boost/multiprecision/cpp_int.hpp>  
  
template<unsigned N>  
using fixed_uint_t =  
    boost::multiprecision::number<boost::multiprecision::cpp_int_backend<  
        N, N, boost::multiprecision::unsigned_magnitude,  
        boost::multiprecision::unchecked, void>>;  
  
enum class Status : uint8_t  
{  
  ok = 0,  
  unknown = 1,  
};  
  
int main()  
{  
  // OK  
  auto b = fixed_uint_t<7>(static_cast<int>(Status::ok));  
  
  // OK  
  int n = static_cast<int>(Status::ok);  
  
  (void) b;  
}  
```

---

## Comment 4

> Username: ckormanyos  
> Created at: 2021-05-17 06:37:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/324#issuecomment-842043732  

> more correct than incorrect  
  
On the other hand, the entirely analagous line for int (initializing `n` below) does _construct_ built-in `int` from the `enum` value, whereas the initializations of and `c` fails, requiring explicit cast of the constructor argument. That could be interpreted as as multiprecision type behaving differently than `int`.  
  
```  
  // OK  
  auto b = fixed_uint_t<7>(static_cast<int>(Status::ok));  
  
  // Not OK!  
  auto c = fixed_uint_t<7>(Status::ok);  
  
  // OK  
  auto n = int(Status::ok);  
```

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-05-17 09:21:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/324#issuecomment-842166843  

As @ckormanyos surmises, this is an entirely unintentional oversight, otherwise we would have had tests ;)  
  
So we need to figure out what the "correct" behaviour should be and proceed from there - the changes are fairly trivial whatever we decide - just adjust the enable_if's.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2021-05-17 09:23:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/324#issuecomment-842168302  

> So we need to figure out what the "correct" behaviour  
  
Yes. I will take a cursory glance through ISO/IEC 14882:2011 through 20 and see if any insight can be gained there.

---

## Comment 7

> Username: ckormanyos  
> Created at: 2021-05-17 11:17:16 UTC  
> Updated at: 2021-05-17 11:17:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/324#issuecomment-842240499  

> will take a cursory glance through ISO/IEC 14882:2011  
  
ISO/IEC 14882:2011 differentiates between scoped and unscoped enumerations.  
  
In Paragraph 7.2, sub-point 9 in the neighborhood of page 159, we find that unscoped enumeration is converted implicitly to `int` by so-called integer promotion, whereby scoped enumerations (such as an enumeration `class`) do not engage in implicit `enum`-to-`int` conversion.

---

## Comment 8

> Username: ckormanyos  
> Created at: 2021-05-17 12:51:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/324#issuecomment-842298761  

> need to figure out what the "correct" behaviour should be and proceed from there  
  
My vote is that Boost should not "on its own" assume to take charge of the implicit conversion of scoped enum to int. And the behaior should remain unchanged as it is in 1.76. This is either less wrong or more right than changing it if you ask me.

---

## Comment 9

> Username: pabristow  
> Created at: 2021-05-17 13:46:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/324#issuecomment-842338273  

I am sure that this is 'more correct', but having taken a well-intended recomendation and converted some unscoped to scoped enums, I paid a price in the annoyingly many changes required to make the code compile and work.  
  
So I agree with @ckormanyos, even if the users may not like it.   
  
C++ enums still don't really work really nicely :-(

---

## Comment 10

> Username: ckormanyos  
> Created at: 2021-05-17 15:30:06 UTC  
> Updated at: 2021-05-17 15:30:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/324#issuecomment-842419924  

> otherwise we would have had tests  
  
OK. So I will add some tests on a branch, both static and runtime, in a few location(s) and we will see how the spectrum of compilers in CI interpret this _expected_ behavior.

---

## Comment 11

> Username: wak-google  
> Created at: 2021-05-17 20:03:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/324#issuecomment-842594698  

Right, but isn't `static_cast<fixed_uint_t<7>>(status)` technically an explicit cast to int from an outside perspective? Shouldn't this be supported then?

---

## Comment 12

> Username: ckormanyos  
> Created at: 2021-05-17 20:37:53 UTC  
> Updated at: 2021-05-17 20:38:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/324#issuecomment-842622420  

> technically an explicit cast to int from an outside perspective  
  
That is a tough one, and although I do know a bit about standards, I need to study up on exactly why not for your very good query.  
  
But I believe it amounts to the same problem --- an object of type `fixed_uint_t<7>` is needed to be created directly from a scoped enum, for which there is no constructor and for which the compiler is not allowed to perform the perhaps in your example intuitively anticipated implicit cast to `int`.
