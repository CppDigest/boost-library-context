# #406 - Boost.Format fails with clang + CUDA [Closed]

> Username: bernhardmgruber  
> Created at: 2021-09-27 13:24:01 UTC  
> Updated at: 2021-09-27 18:26:13 UTC  
> Closed at: 2021-09-27 18:26:13 UTC  
> Url: https://github.com/boostorg/config/issues/406  

This is a spin-off from #297, which @jzmaddock tried to fix in 2fdfd43. With the implemented fix, I can now successfully use Boost.Optional and Boost.Metaparse.  
  
But now I fail to compile Boost.Format with Boost 1.74 (+ the fix in 2fdfd43) when I use clang in CUDA mode. Without 2fdfd43, Boost.Format compiles fine. I could reduce the problem to:  
  
main.cu:  
```c++  
#include <boost/format.hpp>  
```  
compile with:  
```bash  
clang++ -nocudalib --no-cuda-version-check -std=c++17 main.cu  
```  
gives:  
```  
clang: warning: Unknown CUDA version. cuda.h: CUDA_VERSION=11040. Assuming the latest supported version 10.1 [-Wunknown-cuda-version]  
In file included from boost_bug.cu:3:  
In file included from /usr/include/boost/format.hpp:38:  
In file included from /usr/include/boost/format/internals.hpp:21:  
In file included from /usr/include/boost/optional.hpp:15:  
In file included from /usr/include/boost/optional/optional.hpp:43:  
In file included from /usr/include/boost/type_traits/is_base_of.hpp:12:  
/usr/include/boost/type_traits/is_base_and_derived.hpp:142:25: error: invalid application of 'sizeof' to an incomplete type 'boost::in_place_factory_base'  
    BOOST_STATIC_ASSERT(sizeof(B) != 0);  
                        ^~~~~~~~~  
/usr/include/boost/static_assert.hpp:70:55: note: expanded from macro 'BOOST_STATIC_ASSERT'  
#     define BOOST_STATIC_ASSERT( ... ) static_assert(__VA_ARGS__, #__VA_ARGS__)  
                                                      ^~~~~~~~~~~  
/usr/include/boost/type_traits/is_base_and_derived.hpp:217:41: note: in instantiation of template class 'boost::detail::is_base_and_derived_impl2<boost::in_place_factory_base, std::locale>' requested here  
    BOOST_STATIC_CONSTANT(bool, value = bound_type::value);  
                                        ^  
/usr/include/boost/type_traits/is_base_of.hpp:25:31: note: in instantiation of template class 'boost::detail::is_base_and_derived_impl<boost::in_place_factory_base, std::locale>' requested here  
            (::boost::detail::is_base_and_derived_impl<ncvB,ncvD>::value) ||  
                              ^  
/usr/include/boost/type_traits/is_base_of.hpp:31:58: note: in instantiation of template class 'boost::detail::is_base_of_imp<boost::in_place_factory_base, std::locale>' requested here  
      : public integral_constant<bool, (::boost::detail::is_base_of_imp<Base, Derived>::value)> {};  
                                                         ^  
/usr/include/boost/optional/optional.hpp:794:32: note: in instantiation of template class 'boost::is_base_of<boost::in_place_factory_base, std::locale>' requested here  
  : boost::conditional< boost::is_base_of<boost::in_place_factory_base, BOOST_DEDUCED_TYPENAME boost::decay<U>::type>::value  
                               ^  
/usr/include/boost/optional/optional.hpp:823:25: note: in instantiation of template class 'boost::optional_detail::is_convertible_to_T_or_factory<std::locale, const std::locale &>' requested here  
  : boost::conditional< is_convertible_to_T_or_factory<T, U>::value  
                        ^  
/usr/include/boost/core/enable_if.hpp:41:41: note: in instantiation of template class 'boost::optional_detail::is_optional_val_init_candidate<std::locale, const std::locale &, false>' requested here  
  struct enable_if : public enable_if_c<Cond::value, T> {};  
                                        ^  
/usr/include/boost/optional/optional.hpp:951:53: note: in instantiation of template class 'boost::enable_if<boost::optional_detail::is_optional_val_init_candidate<std::locale, const std::locale &, false>, bool>' requested here  
                      BOOST_DEDUCED_TYPENAME boost::enable_if< optional_detail::is_optional_val_init_candidate<T, Expr>, bool>::type = true  
                                                    ^  
/usr/include/boost/format/format_implementation.hpp:40:44: note: while substituting deduced template arguments into function template 'optional' [with Expr = const std::locale &]  
          exceptions_(io::all_error_bits), loc_(loc)  
                                           ^  
/usr/include/boost/optional/detail/optional_factory_support.hpp:32:9: note: forward declaration of 'boost::in_place_factory_base'  
  class in_place_factory_base ;  
        ^  
In file included from boost_bug.cu:3:  
In file included from /usr/include/boost/format.hpp:38:  
In file included from /usr/include/boost/format/internals.hpp:21:  
In file included from /usr/include/boost/optional.hpp:15:  
In file included from /usr/include/boost/optional/optional.hpp:43:  
In file included from /usr/include/boost/type_traits/is_base_of.hpp:12:  
/usr/include/boost/type_traits/is_base_and_derived.hpp:142:25: error: invalid application of 'sizeof' to an incomplete type 'boost::typed_in_place_factory_base'  
    BOOST_STATIC_ASSERT(sizeof(B) != 0);  
                        ^~~~~~~~~  
/usr/include/boost/static_assert.hpp:70:55: note: expanded from macro 'BOOST_STATIC_ASSERT'  
#     define BOOST_STATIC_ASSERT( ... ) static_assert(__VA_ARGS__, #__VA_ARGS__)  
                                                      ^~~~~~~~~~~  
/usr/include/boost/type_traits/is_base_and_derived.hpp:217:41: note: in instantiation of template class 'boost::detail::is_base_and_derived_impl2<boost::typed_in_place_factory_base, std::locale>' requested here  
    BOOST_STATIC_CONSTANT(bool, value = bound_type::value);  
                                        ^  
/usr/include/boost/type_traits/is_base_of.hpp:25:31: note: in instantiation of template class 'boost::detail::is_base_and_derived_impl<boost::typed_in_place_factory_base, std::locale>' requested here  
            (::boost::detail::is_base_and_derived_impl<ncvB,ncvD>::value) ||  
                              ^  
/usr/include/boost/type_traits/is_base_of.hpp:31:58: note: in instantiation of template class 'boost::detail::is_base_of_imp<boost::typed_in_place_factory_base, std::locale>' requested here  
      : public integral_constant<bool, (::boost::detail::is_base_of_imp<Base, Derived>::value)> {};  
                                                         ^  
/usr/include/boost/optional/optional.hpp:795:32: note: in instantiation of template class 'boost::is_base_of<boost::typed_in_place_factory_base, std::locale>' requested here  
                     || boost::is_base_of<boost::typed_in_place_factory_base, BOOST_DEDUCED_TYPENAME boost::decay<U>::type>::value  
                               ^  
/usr/include/boost/optional/optional.hpp:823:25: note: in instantiation of template class 'boost::optional_detail::is_convertible_to_T_or_factory<std::locale, const std::locale &>' requested here  
  : boost::conditional< is_convertible_to_T_or_factory<T, U>::value  
                        ^  
/usr/include/boost/core/enable_if.hpp:41:41: note: in instantiation of template class 'boost::optional_detail::is_optional_val_init_candidate<std::locale, const std::locale &, false>' requested here  
  struct enable_if : public enable_if_c<Cond::value, T> {};  
                                        ^  
/usr/include/boost/optional/optional.hpp:951:53: note: in instantiation of template class 'boost::enable_if<boost::optional_detail::is_optional_val_init_candidate<std::locale, const std::locale &, false>, bool>' requested here  
                      BOOST_DEDUCED_TYPENAME boost::enable_if< optional_detail::is_optional_val_init_candidate<T, Expr>, bool>::type = true  
                                                    ^  
/usr/include/boost/format/format_implementation.hpp:40:44: note: while substituting deduced template arguments into function template 'optional' [with Expr = const std::locale &]  
          exceptions_(io::all_error_bits), loc_(loc)  
                                           ^  
/usr/include/boost/optional/detail/optional_factory_support.hpp:33:9: note: forward declaration of 'boost::typed_in_place_factory_base'  
  class typed_in_place_factory_base ;  
        ^  
2 errors generated when compiling for sm_20.  
```  
  
So I guess my issue is now to get this program working with clang + CUDA:  
```c++  
#include <boost/optional.hpp>  
#include <boost/metaparse.hpp>  
#include <boost/format.hpp>  
  
using T = BOOST_METAPARSE_STRING("asfd");  
```  
Could you please have another look? Thank you!

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-09-27 18:14:05 UTC  
> Url: https://github.com/boostorg/config/issues/406#issuecomment-928151025  

Missing #includes, whether in optional or format I'm not sure, adding  
  
#include <boost/utility/in_place_factory.hpp>  
#include <boost/utility/typed_in_place_factory.hpp>  
  
Before including format or optional fixes things for me: https://godbolt.org/z/MfrWhsYhq.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-09-27 18:26:13 UTC  
> Url: https://github.com/boostorg/config/issues/406#issuecomment-928160014  

Moved to: https://github.com/boostorg/optional/issues/97
