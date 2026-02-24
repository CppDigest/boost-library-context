# #506 - Error in powm for cpp_int [Closed]

> Username: watason  
> Created at: 2022-11-30 04:44:36 UTC  
> Updated at: 2022-12-03 19:39:31 UTC  
> Closed at: 2022-12-03 19:39:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/506  

Hi.   
I am trying to find out what is the error in cpp_int powm.  
I ran this code and got the following compile error  
I didn't understand how to deal with it or what caused it, so please let me know.  
The execution environment is wandbox,c++17  
(I got the same error on macos,c++17)  
  
```　#include <boost/multiprecision/cpp_int.hpp>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
using namespace std;  
namespace mp = boost::multiprecision;  
  
int main()  
{  
    mp::cpp_int ten(10);  
    mp::cpp_int ran(5);  
    auto pten = mp::powm(ten,5,13);  
    // auto pmod = pten;   //OK  
    //auto pmod = pten * 5;  // ERROR  
    auto pmod = pten * ran; // ERROR   
    cout << pmod << endl;  
    return 0;  
}　  
```  
  
```  
In file included from /opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/cpp_int.hpp:17,  
                 from prog.cc:1:  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp: In instantiation of 'constexpr bool boost::multiprecision::number<Backend, ExpressionTemplates>::contains_self(const Exp&) const [with Exp = boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::default_ops::powm_func, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, int, int>; Backend = boost::multiprecision::backends::cpp_int_backend<>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_on]':  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:1354:30:   required from 'constexpr void boost::multiprecision::number<Backend, ExpressionTemplates>::do_assign(const Exp&, const boost::multiprecision::detail::multiplies&) [with Exp = boost::multiprecision::detail::expression<boost::multiprecision::detail::multiplies, boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::default_ops::powm_func, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, int, int>, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, void, void>; Backend = boost::multiprecision::backends::cpp_int_backend<>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_on]'  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:1213:16:   required from 'constexpr void boost::multiprecision::number<Backend, ExpressionTemplates>::do_assign(const boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>&, const std::integral_constant<bool, true>&) [with tag = boost::multiprecision::detail::multiplies; Arg1 = boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::default_ops::powm_func, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, int, int>; Arg2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >; Arg3 = void; Arg4 = void; Backend = boost::multiprecision::backends::cpp_int_backend<>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_on]'  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:315:16:   required from 'constexpr typename std::enable_if<std::is_convertible<typename boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type, boost::multiprecision::number<Backend, ExpressionTemplates> >::value, boost::multiprecision::number<Backend, ExpressionTemplates>&>::type boost::multiprecision::number<Backend, ExpressionTemplates>::operator=(const boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>&) [with tag = boost::multiprecision::detail::multiplies; Arg1 = boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::default_ops::powm_func, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, int, int>; Arg2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >; Arg3 = void; Arg4 = void; Backend = boost::multiprecision::backends::cpp_int_backend<>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_on; typename std::enable_if<std::is_convertible<typename boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type, boost::multiprecision::number<Backend, ExpressionTemplates> >::value, boost::multiprecision::number<Backend, ExpressionTemplates>&>::type = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >&; typename boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >]'  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:431:7:   required from 'constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>&, typename std::enable_if<std::is_convertible<typename boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type, boost::multiprecision::number<Backend, ExpressionTemplates> >::value>::type*) [with tag = boost::multiprecision::detail::multiplies; Arg1 = boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::default_ops::powm_func, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, int, int>; Arg2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >; Arg3 = void; Arg4 = void; Backend = boost::multiprecision::backends::cpp_int_backend<>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_on; typename std::enable_if<std::is_convertible<typename boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type, boost::multiprecision::number<Backend, ExpressionTemplates> >::value>::type = void; typename boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >]'  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:2249:66:   required from 'std::ostream& boost::multiprecision::detail::operator<<(std::ostream&, const boost::multiprecision::detail::expression<tag, Arg1, Arg2, Arg3, Arg4>&) [with tag = boost::multiprecision::detail::multiplies; A1 = boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::default_ops::powm_func, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, int, int>; A2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >; A3 = void; A4 = void; std::ostream = std::basic_ostream<char>]'  
prog.cc:15:13:   required from here  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:2140:27: error: no matching function for call to 'boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >::contains_self(const boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::default_ops::powm_func, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, int, int>&, boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::default_ops::powm_func, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, int, int>::arity) const'  
 2140 |       return contains_self(e, typename Exp::arity());  
      |              ~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:2138:55: note: candidate: 'template<class Exp> constexpr bool boost::multiprecision::number<Backend, ExpressionTemplates>::contains_self(const Exp&) const [with Exp = Exp; Backend = boost::multiprecision::backends::cpp_int_backend<>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_on]'  
 2138 |    BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR bool contains_self(const Exp& e) const noexcept  
      |                                                       ^~~~~~~~~~~~~  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:2138:55: note:   template argument deduction/substitution failed:  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:2140:27: note:   candidate expects 1 argument, 2 provided  
 2140 |       return contains_self(e, typename Exp::arity());  
      |              ~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:2143:55: note: candidate: 'template<class Exp> constexpr bool boost::multiprecision::number<Backend, ExpressionTemplates>::contains_self(const Exp&, const std::integral_constant<int, 0>&) const [with Exp = Exp; Backend = boost::multiprecision::backends::cpp_int_backend<>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_on]'  
 2143 |    BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR bool contains_self(const Exp& e, std::integral_constant<int, 0> const&) const noexcept  
      |                                                       ^~~~~~~~~~~~~  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:2143:55: note:   template argument deduction/substitution failed:  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:2140:45: note:   cannot convert 'boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::default_ops::powm_func, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, int, int>::arity()' (type 'boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::default_ops::powm_func, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, int, int>::arity' {aka 'std::integral_constant<int, 4>'}) to type 'const std::integral_constant<int, 0>&'  
 2140 |       return contains_self(e, typename Exp::arity());  
      |                                             ^~~~~~~  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:2148:55: note: candidate: 'template<class Exp> constexpr bool boost::multiprecision::number<Backend, ExpressionTemplates>::contains_self(const Exp&, const std::integral_constant<int, 1>&) const [with Exp = Exp; Backend = boost::multiprecision::backends::cpp_int_backend<>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_on]'  
 2148 |    BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR bool contains_self(const Exp& e, std::integral_constant<int, 1> const&) const noexcept  
      |                                                       ^~~~~~~~~~~~~  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:2148:55: note:   template argument deduction/substitution failed:  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:2140:45: note:   cannot convert 'boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::default_ops::powm_func, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, int, int>::arity()' (type 'boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::default_ops::powm_func, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, int, int>::arity' {aka 'std::integral_constant<int, 4>'}) to type 'const std::integral_constant<int, 1>&'  
 2140 |       return contains_self(e, typename Exp::arity());  
      |                                             ^~~~~~~  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:2154:55: note: candidate: 'template<class Exp> constexpr bool boost::multiprecision::number<Backend, ExpressionTemplates>::contains_self(const Exp&, const std::integral_constant<int, 2>&) const [with Exp = Exp; Backend = boost::multiprecision::backends::cpp_int_backend<>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_on]'  
 2154 |    BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR bool contains_self(const Exp& e, std::integral_constant<int, 2> const&) const noexcept  
      |                                                       ^~~~~~~~~~~~~  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:2154:55: note:   template argument deduction/substitution failed:  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:2140:45: note:   cannot convert 'boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::default_ops::powm_func, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, int, int>::arity()' (type 'boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::default_ops::powm_func, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, int, int>::arity' {aka 'std::integral_constant<int, 4>'}) to type 'const std::integral_constant<int, 2>&'  
 2140 |       return contains_self(e, typename Exp::arity());  
      |                                             ^~~~~~~  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:2161:55: note: candidate: 'template<class Exp> constexpr bool boost::multiprecision::number<Backend, ExpressionTemplates>::contains_self(const Exp&, const std::integral_constant<int, 3>&) const [with Exp = Exp; Backend = boost::multiprecision::backends::cpp_int_backend<>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_on]'  
 2161 |    BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR bool contains_self(const Exp& e, std::integral_constant<int, 3> const&) const noexcept  
      |                                                       ^~~~~~~~~~~~~  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:2161:55: note:   template argument deduction/substitution failed:  
/opt/wandbox/boost-1.79.0-gcc-11.1.0/include/boost/multiprecision/number.hpp:2140:45: note:   cannot convert 'boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::default_ops::powm_func, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, int, int>::arity()' (type 'boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::default_ops::powm_func, boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<> >, int, int>::arity' {aka 'std::integral_constant<int, 4>'}) to type 'const std::integral_constant<int, 3>&'  
 2140 |       return contains_self(e, typename Exp::arity());  
      |                                             ^~~~~~~  
```  
  
Thank you.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-12-01 17:46:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/506#issuecomment-1334130940  

Confirmed as a bug: powm is the only function returning an expression template with that number of args, and I missed the `contains_self` overload for that case.  
  
However, PLEASE do not use auto in conjunction with expression templates: it will lead to dangling references in some cases, see https://www.boost.org/doc/libs/1_80_0/libs/multiprecision/doc/html/boost_multiprecision/intro.html#boost_multiprecision.intro.expression_templates  
  
Will fix shortly.

---

## Comment 2

> Username: watason  
> Created at: 2022-12-01 23:49:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/506#issuecomment-1334589465  

@jzmaddock   
Thanks all for the lighting fast response!  
  
> However, PLEASE do not use auto in conjunction with expression templates: it will lead to dangling references in some cases, see  
  
You're absolutely right, thank you.  
Please take care of the correspondence.
