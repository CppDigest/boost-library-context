# #25 Fix ill-formed constant expression errors [Closed]

> Username: stbergmann  
> Created at: 2022-08-09 11:39:53 UTC  
> Updated at: 2022-11-16 20:44:29 UTC  
> Closed at: 2022-11-16 20:44:29 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/25  

...with recent Clang 16 trunk since <https://github.com/llvm/llvm-project/commit/b3645353041818f61e2580635409ddb81ff5a272> "[Clang] Diagnose ill-formed constant expression when setting a non fixed enum to a value outside the range of the enumeration values", causing  
```  
$ cat test.cc  
#include "boost/numeric/conversion/cast.hpp"  
int main() { return boost::numeric_cast<int>(0L); }  
  
$ clang++ test.cc  
In file included from test.cc:1:  
In file included from /usr/include/boost/numeric/conversion/cast.hpp:33:  
In file included from /usr/include/boost/numeric/conversion/converter.hpp:13:  
In file included from /usr/include/boost/numeric/conversion/conversion_traits.hpp:13:  
In file included from /usr/include/boost/numeric/conversion/detail/conversion_traits.hpp:18:  
In file included from /usr/include/boost/numeric/conversion/detail/int_float_mixture.hpp:19:  
In file included from /usr/include/boost/mpl/integral_c.hpp:32:  
/usr/include/boost/mpl/aux_/integral_wrapper.hpp:73:31: error: non-type template argument is not a constant expression  
    typedef AUX_WRAPPER_INST( BOOST_MPL_AUX_STATIC_CAST(AUX_WRAPPER_VALUE_TYPE, (value - 1)) ) prior;  
            ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/mpl/aux_/static_cast.hpp:24:47: note: expanded from macro 'BOOST_MPL_AUX_STATIC_CAST'  
#   define BOOST_MPL_AUX_STATIC_CAST(T, expr) static_cast<T>(expr)  
                                              ^  
/usr/include/boost/mpl/integral_c.hpp:31:54: note: expanded from macro 'AUX_WRAPPER_INST'  
#define AUX_WRAPPER_INST(value) AUX_WRAPPER_NAME< T, value >  
                                                     ^~~~~  
/usr/include/boost/numeric/conversion/detail/meta.hpp:30:46: note: in instantiation of template class 'mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>' requested here  
       enum { x = ( BOOST_MPL_AUX_VALUE_WKND(T1)::value == BOOST_MPL_AUX_VALUE_WKND(T2)::value ) };  
                                             ^  
/usr/include/boost/mpl/if.hpp:63:68: note: in instantiation of template class 'boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>>' requested here  
          BOOST_MPL_AUX_STATIC_CAST(bool, BOOST_MPL_AUX_VALUE_WKND(T1)::value)  
                                                                   ^  
/usr/include/boost/mpl/eval_if.hpp:37:22: note: in instantiation of template class 'boost::mpl::if_<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>>, boost::mpl::identity<boost::numeric::convdetail::get_subranged_BuiltIn2BuiltIn<int, long>>, boost::mpl::eval_if<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_udt>>, boost::mpl::identity<boost::mpl::identity<boost::numeric::convdetail::subranged_BuiltIn2Udt<int, long>>>, boost::mpl::if_<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::udt_to_builtin>>, boost::mpl::identity<boost::numeric::convdetail::subranged_Udt2BuiltIn<int, long>>, boost::mpl::identity<boost::numeric::convdetail::subranged_Udt2Udt<int, long>>>>>' requested here  
    typedef typename if_<C,F1,F2>::type f_;  
                     ^  
/usr/include/boost/numeric/conversion/detail/meta.hpp:81:12: note: in instantiation of template class 'boost::mpl::eval_if<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>>, boost::mpl::identity<boost::numeric::convdetail::get_subranged_BuiltIn2BuiltIn<int, long>>, boost::mpl::eval_if<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_udt>>, boost::mpl::identity<boost::mpl::identity<boost::numeric::convdetail::subranged_BuiltIn2Udt<int, long>>>, boost::mpl::if_<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::udt_to_builtin>>, boost::mpl::identity<boost::numeric::convdetail::subranged_Udt2BuiltIn<int, long>>, boost::mpl::identity<boost::numeric::convdetail::subranged_Udt2Udt<int, long>>>>>' requested here  
      mpl::eval_if<is_case0,Case0TypeQ,choose_1_2_3Q>::type  
           ^  
/usr/include/boost/numeric/conversion/detail/udt_builtin_mixture.hpp:41:7: note: in instantiation of template class 'boost::numeric::convdetail::ct_switch4<mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_udt>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::udt_to_builtin>, boost::numeric::convdetail::get_subranged_BuiltIn2BuiltIn<int, long>, boost::mpl::identity<boost::numeric::convdetail::subranged_BuiltIn2Udt<int, long>>, boost::mpl::identity<boost::numeric::convdetail::subranged_Udt2BuiltIn<int, long>>, boost::mpl::identity<boost::numeric::convdetail::subranged_Udt2Udt<int, long>>>' requested here  
      ct_switch4<UdtMixture  
      ^  
/usr/include/boost/numeric/conversion/detail/is_subranged.hpp:205:9: note: (skipping 3 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
        for_udt_builtin_mixture<udt_builtin_mixture, BuiltIn2BuiltInQ, BuiltIn2UdtQ, Udt2BuiltInQ, Udt2UdtQ>::type  
        ^  
/usr/include/boost/numeric/conversion/conversion_traits.hpp:22:7: note: in instantiation of template class 'boost::numeric::convdetail::non_trivial_traits_impl<int, long>' requested here  
    : convdetail::get_conversion_traits<T,S>::type  
      ^  
/usr/include/boost/numeric/conversion/detail/converter.hpp:584:22: note: in instantiation of template class 'boost::numeric::conversion_traits<int, long>' requested here  
    typedef typename Traits::trivial trivial ;  
                     ^  
/usr/include/boost/numeric/conversion/converter.hpp:29:32: note: in instantiation of template class 'boost::numeric::convdetail::get_converter_impl<boost::numeric::conversion_traits<int, long>, boost::numeric::def_overflow_handler, boost::numeric::Trunc<long>, boost::numeric::raw_converter<boost::numeric::conversion_traits<int, long>>, boost::numeric::UseInternalRangeChecker>' requested here  
struct converter : convdetail::get_converter_impl<Traits,  
                               ^  
/usr/include/boost/numeric/conversion/cast.hpp:53:16: note: in instantiation of template class 'boost::numeric::converter<int, long, boost::numeric::conversion_traits<int, long>, boost::numeric::def_overflow_handler, boost::numeric::Trunc<long>>' requested here  
        return converter::convert(arg);  
               ^  
test.cc:2:28: note: in instantiation of function template specialization 'boost::numeric_cast<int, long>' requested here  
int main() { return boost::numeric_cast<int>(0L); }  
                           ^  
/usr/include/boost/mpl/aux_/integral_wrapper.hpp:73:31: note: integer value -1 is outside the valid range of values [0, 3] for enumeration type 'boost::numeric::udt_builtin_mixture_enum'  
    typedef AUX_WRAPPER_INST( BOOST_MPL_AUX_STATIC_CAST(AUX_WRAPPER_VALUE_TYPE, (value - 1)) ) prior;  
                              ^  
/usr/include/boost/mpl/aux_/static_cast.hpp:24:47: note: expanded from macro 'BOOST_MPL_AUX_STATIC_CAST'  
#   define BOOST_MPL_AUX_STATIC_CAST(T, expr) static_cast<T>(expr)  
                                              ^  
```  
etc.  
  
(I originally ran into this when building LibreOffice, see <https://git.libreoffice.org/core/+/ff2a90379fc14cc30e60e829ed20752b8c2fa02a%5E!> "external/boost: Fix ill-formed constant expression errors".)

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-11-16 00:57:21 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/25#issuecomment-1316110033  

This would break C++03 if I'm not mistaken.

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-11-16 08:48:49 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/25#issuecomment-1316616602  

Discussion in https://github.com/boostorg/numeric_conversion/issues/24.

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-11-16 20:44:29 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/25#issuecomment-1317645968  

Fixed with https://github.com/boostorg/numeric_conversion/commit/50a1eae942effb0a9b90724323ef8f2a67e7984a.

---
