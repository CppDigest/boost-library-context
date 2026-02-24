# #934 - tag_invoke cannot use templates in value_to_tag [Closed]

> Username: tobias-loew  
> Created at: 2023-08-27 18:24:32 UTC  
> Updated at: 2023-08-28 14:08:37 UTC  
> Closed at: 2023-08-28 14:04:33 UTC  
> Url: https://github.com/boostorg/json/issues/934  

### Version of Boost  
  
since 1.82  
  
### Steps necessary to reproduce the problem  
  
code like:  
```  
	template<typename T>  
	some_type<T> tag_invoke(const boost::json::value_to_tag<some_type<T>>&, boost::json::value const& jv) {  
        ...  
        }      
```  
  
no longer works.  
This seems to be a consequence of   
  
 - _value_to supports missing elements for std::optional._   
  
Could this be implemented differently to re-enable type-deduction for the above cases?  
  
### All relevant compiler information

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-08-27 18:55:36 UTC  
> Url: https://github.com/boostorg/json/issues/934#issuecomment-1694736672  

Seems to work for me: https://godbolt.org/z/bY1x1fhff  
  
So, I need more information than what you have provided. Ideally a Minimal Reproducible Example.

---

## Comment 2

> Username: tobias-loew  
> Created at: 2023-08-28 08:55:03 UTC  
> Url: https://github.com/boostorg/json/issues/934#issuecomment-1695303588  

I had a closer look: The structure of my code is slightly more complicated.  
```  
#include <variant>  
#include <boost/json/value_to.hpp>  
  
  
template< class T > struct some_base_type {  
};  
  
template<typename T>  
using some_type = std::variant<some_base_type<T>, int, char const*>;  
  
  
template< class T >  
some_type<T>  
tag_invoke(  
    boost::json::value_to_tag< some_type<T> > const&,  
    boost::json::value const& jv)  
{  
    boost::json::string const* str = jv.if_string();  
    if (str && *str == "T11")  
        return some_type<T>{""};  
    throw std::invalid_argument("!");  
}  
  
  
some_type<int> foo(boost::json::value jv)  
{  
    return boost::json::value_to< some_type<int> >(jv);  
}  
```  
  
 I use VS 2022 with MSVC 143 /std:c++latest  
This compiles with 1.81 (see https://godbolt.org/z/TjvfYsaTb  vcpkg is still on 1,81) but fails with >= 1.82.  
Here is my error log:  
```  
Build started...  
1>------ Build started: Project: ConsoleApplication3, Configuration: Debug x64 ------  
1>ConsoleApplication3.cpp  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(230,20): error C2672: 'value_to_impl': no matching overloaded function found  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(813,1): message : could be 'T boost::json::detail::value_to_impl(Impl,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(230,20): message : 'T boost::json::detail::value_to_impl(Impl,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)': could not deduce template argument for 'boost::json::value_to_tag<T>' from 'boost::json::try_value_to_tag<T>'  
1>        with  
1>        [  
1>            T=bare_T  
1>        ]  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(803,1): message : or       'T boost::json::detail::value_to_impl(boost::json::detail::no_conversion_tag,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(230,20): message : 'T boost::json::detail::value_to_impl(boost::json::detail::no_conversion_tag,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)': could not deduce template argument for 'boost::json::value_to_tag<T>' from 'boost::json::try_value_to_tag<T>'  
1>        with  
1>        [  
1>            T=bare_T  
1>        ]  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(786,1): message : or       'detail::mp_if_c_impl<!boost::mp11::mp_valid<boost::json::detail::has_nonthrowing_full_context_conversion_to_impl,Sup::type,T>::value,boost::system::result<T,boost::system::error_code>,>::type boost::json::detail::value_to_impl(boost::json::detail::full_context_conversion_tag,boost::json::try_value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::full_context_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(765,1): message : or       'detail::mp_if_c_impl<!boost::mp11::mp_valid<boost::json::detail::has_nonthrowing_context_conversion_to_impl,Sup::type,T>::value,boost::system::result<T,boost::system::error_code>,>::type boost::json::detail::value_to_impl(boost::json::detail::context_conversion_tag,boost::json::try_value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::context_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(747,1): message : or       'detail::mp_if_c_impl<!boost::mp11::mp_valid<boost::json::detail::has_nonthrowing_user_conversion_to_impl,T>::value,boost::system::result<T,boost::system::error_code>,>::type boost::json::detail::value_to_impl(boost::json::detail::user_conversion_tag,boost::json::try_value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::user_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(682,1): message : or       'detail::mp_if_c_impl<boost::mp11::detail::mp_valid_impl<boost::json::detail::has_nonthrowing_full_context_conversion_to_impl,Sup::type,T>::type::value,boost::system::result<T,boost::system::error_code>,>::type boost::json::detail::value_to_impl(boost::json::detail::full_context_conversion_tag,boost::json::try_value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::full_context_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(662,1): message : or       'detail::mp_if_c_impl<boost::mp11::detail::mp_valid_impl<boost::json::detail::has_nonthrowing_context_conversion_to_impl,Sup::type,T>::type::value,boost::system::result<T,boost::system::error_code>,>::type boost::json::detail::value_to_impl(boost::json::detail::context_conversion_tag,boost::json::try_value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::context_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(647,1): message : or       'detail::mp_if_c_impl<boost::mp11::detail::mp_valid_impl<boost::json::detail::has_nonthrowing_user_conversion_to_impl,T>::type::value,boost::system::result<T,boost::system::error_code>,>::type boost::json::detail::value_to_impl(boost::json::detail::user_conversion_tag,boost::json::try_value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::user_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(626,1): message : or       'detail::mp_if_c_impl<!boost::mp11::mp_valid<boost::json::detail::has_full_context_conversion_to_impl,Sup::type,T>::value,T,>::type boost::json::detail::value_to_impl(boost::json::detail::full_context_conversion_tag,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(230,20): message : 'detail::mp_if_c_impl<!boost::mp11::mp_valid<boost::json::detail::has_full_context_conversion_to_impl,Sup::type,T>::value,T,>::type boost::json::detail::value_to_impl(boost::json::detail::full_context_conversion_tag,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)': could not deduce template argument for 'boost::json::value_to_tag<T>' from 'boost::json::try_value_to_tag<T>'  
1>        with  
1>        [  
1>            T=bare_T  
1>        ]  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(586,1): message : or       'detail::mp_if_c_impl<!boost::mp11::mp_valid<boost::json::detail::has_context_conversion_to_impl,Sup::type,T>::value,T,>::type boost::json::detail::value_to_impl(boost::json::detail::context_conversion_tag,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(230,20): message : 'detail::mp_if_c_impl<!boost::mp11::mp_valid<boost::json::detail::has_context_conversion_to_impl,Sup::type,T>::value,T,>::type boost::json::detail::value_to_impl(boost::json::detail::context_conversion_tag,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)': could not deduce template argument for 'boost::json::value_to_tag<T>' from 'boost::json::try_value_to_tag<T>'  
1>        with  
1>        [  
1>            T=bare_T  
1>        ]  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(568,1): message : or       'detail::mp_if_c_impl<!boost::mp11::mp_valid<boost::json::detail::has_user_conversion_to_impl,T>::value,T,>::type boost::json::detail::value_to_impl(boost::json::detail::user_conversion_tag,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(230,20): message : 'detail::mp_if_c_impl<!boost::mp11::mp_valid<boost::json::detail::has_user_conversion_to_impl,T>::value,T,>::type boost::json::detail::value_to_impl(boost::json::detail::user_conversion_tag,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)': could not deduce template argument for 'boost::json::value_to_tag<T>' from 'boost::json::try_value_to_tag<T>'  
1>        with  
1>        [  
1>            T=bare_T  
1>        ]  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(555,1): message : or       'detail::mp_if_c_impl<boost::mp11::detail::mp_valid_impl<boost::json::detail::has_full_context_conversion_to_impl,Sup::type,T>::type::value,T,>::type boost::json::detail::value_to_impl(boost::json::detail::full_context_conversion_tag,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(230,20): message : 'detail::mp_if_c_impl<boost::mp11::detail::mp_valid_impl<boost::json::detail::has_full_context_conversion_to_impl,Sup::type,T>::type::value,T,>::type boost::json::detail::value_to_impl(boost::json::detail::full_context_conversion_tag,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)': could not deduce template argument for 'boost::json::value_to_tag<T>' from 'boost::json::try_value_to_tag<T>'  
1>        with  
1>        [  
1>            T=bare_T  
1>        ]  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(537,1): message : or       'detail::mp_if_c_impl<boost::mp11::detail::mp_valid_impl<boost::json::detail::has_context_conversion_to_impl,Sup::type,T>::type::value,T,>::type boost::json::detail::value_to_impl(boost::json::detail::context_conversion_tag,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(230,20): message : 'detail::mp_if_c_impl<boost::mp11::detail::mp_valid_impl<boost::json::detail::has_context_conversion_to_impl,Sup::type,T>::type::value,T,>::type boost::json::detail::value_to_impl(boost::json::detail::context_conversion_tag,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)': could not deduce template argument for 'boost::json::value_to_tag<T>' from 'boost::json::try_value_to_tag<T>'  
1>        with  
1>        [  
1>            T=bare_T  
1>        ]  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(524,1): message : or       'detail::mp_if_c_impl<boost::mp11::detail::mp_valid_impl<boost::json::detail::has_user_conversion_to_impl,T>::type::value,T,>::type boost::json::detail::value_to_impl(boost::json::detail::user_conversion_tag,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(230,20): message : 'detail::mp_if_c_impl<boost::mp11::detail::mp_valid_impl<boost::json::detail::has_user_conversion_to_impl,T>::type::value,T,>::type boost::json::detail::value_to_impl(boost::json::detail::user_conversion_tag,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)': could not deduce template argument for 'boost::json::value_to_tag<T>' from 'boost::json::try_value_to_tag<T>'  
1>        with  
1>        [  
1>            T=bare_T  
1>        ]  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(492,1): message : or       'boost::system::result<T,boost::system::error_code> boost::json::detail::value_to_impl(boost::json::detail::described_enum_conversion_tag,boost::json::try_value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::described_enum_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(452,1): message : or       'boost::system::result<T,boost::system::error_code> boost::json::detail::value_to_impl(boost::json::detail::described_class_conversion_tag,boost::json::try_value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::described_class_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(353,1): message : or       'boost::system::result<T,boost::system::error_code> boost::json::detail::value_to_impl(boost::json::detail::tuple_conversion_tag,boost::json::try_value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::tuple_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(285,1): message : or       'boost::system::result<T,boost::system::error_code> boost::json::detail::value_to_impl(boost::json::detail::sequence_conversion_tag,boost::json::try_value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::sequence_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(245,1): message : or       'boost::system::result<T,boost::system::error_code> boost::json::detail::value_to_impl(boost::json::detail::map_like_conversion_tag,boost::json::try_value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::map_like_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(228,1): message : or       'boost::system::result<T,boost::system::error_code> boost::json::detail::value_to_impl(boost::json::detail::string_like_conversion_tag,boost::json::try_value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::string_like_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(212,1): message : or       'boost::system::result<T,boost::system::error_code> boost::json::detail::value_to_impl(boost::json::detail::null_like_conversion_tag,boost::json::try_value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::null_like_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(199,1): message : or       'boost::system::result<T,boost::system::error_code> boost::json::detail::value_to_impl(boost::json::detail::number_conversion_tag,boost::json::try_value_to_tag<T>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::number_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(185,1): message : or       'boost::system::result<bool,boost::system::error_code> boost::json::detail::value_to_impl(boost::json::detail::bool_conversion_tag,boost::json::try_value_to_tag<bool>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::bool_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(168,1): message : or       'boost::system::result<boost::json::string,boost::system::error_code> boost::json::detail::value_to_impl(boost::json::detail::string_conversion_tag,boost::json::try_value_to_tag<boost::json::string>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::string_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(151,1): message : or       'boost::system::result<boost::json::array,boost::system::error_code> boost::json::detail::value_to_impl(boost::json::detail::array_conversion_tag,boost::json::try_value_to_tag<boost::json::array>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::array_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(134,1): message : or       'boost::system::result<boost::json::object,boost::system::error_code> boost::json::detail::value_to_impl(boost::json::detail::object_conversion_tag,boost::json::try_value_to_tag<boost::json::object>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::object_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(125,1): message : or       'boost::json::value boost::json::detail::value_to_impl(boost::json::detail::value_conversion_tag,boost::json::value_to_tag<boost::json::value>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::value_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(114,1): message : or       'boost::system::result<boost::json::value,boost::system::error_code> boost::json::detail::value_to_impl(boost::json::detail::value_conversion_tag,boost::json::try_value_to_tag<boost::json::value>,const boost::json::value &,const Ctx &)'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : 'initializing': cannot convert from 'cat' to 'boost::json::detail::value_conversion_tag'  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(231,12): message : No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(875,24): message : see reference to function template instantiation 'boost::system::result<some_base_type<int>,boost::system::error_code> boost::json::try_value_to<T,Ctx2>(const boost::json::value &,const Context &)' being compiled  
1>        with  
1>        [  
1>            Ctx2=std::tuple<boost::json::detail::allow_exceptions,boost::json::detail::no_context>,  
1>            Context=std::tuple<boost::json::detail::allow_exceptions,boost::json::detail::no_context>  
1>        ]  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\mp11\algorithm.hpp(1060,28): message : see reference to function template instantiation 'void boost::json::tag_invoke::<lambda_1>::operator ()<std::integral_constant<size_t,0>>(_T1) const' being compiled  
1>        with  
1>        [  
1>            _T1=std::integral_constant<size_t,0>  
1>        ]  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\mp11\algorithm.hpp(1076,20): message : see reference to function template instantiation 'F boost::mp11::detail::mp_for_each_impl<std::integral_constant<size_t,0>,std::integral_constant<unsigned __int64,1>,std::integral_constant<size_t,2>,_Ty>(boost::mp11::mp_list<std::integral_constant<size_t,0>,std::integral_constant<unsigned __int64,1>,std::integral_constant<size_t,2>>,F &&)' being compiled  
1>        with  
1>        [  
1>            F=boost::json::tag_invoke::<lambda_1>,  
1>            _Ty=boost::json::tag_invoke::<lambda_1>  
1>        ]  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(870,11): message : see reference to function template instantiation 'boost::json::tag_invoke::<lambda_1> boost::mp11::mp_for_each<boost::mp11::mp_list<std::integral_constant<size_t,0>,std::integral_constant<unsigned __int64,1>,std::integral_constant<size_t,2>>,boost::json::tag_invoke::<lambda_1>>(F &&)' being compiled  
1>        with  
1>        [  
1>            F=boost::json::tag_invoke::<lambda_1>  
1>        ]  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\detail\value_to.hpp(632,16): message : see reference to function template instantiation 'boost::system::result<std::variant<some_base_type<int>,int,const char *>,boost::system::error_code> boost::json::tag_invoke<some_base_type<int>,int,const char*,boost::json::detail::no_context,std::tuple<boost::json::detail::allow_exceptions,Ctx>>(boost::json::try_value_to_tag<T>,const boost::json::value &,const Ctx1 &,const Ctx2 &)' being compiled  
1>        with  
1>        [  
1>            Ctx=boost::json::detail::no_context,  
1>            T=bare_T,  
1>            Ctx1=boost::json::detail::no_context,  
1>            Ctx2=std::tuple<boost::json::detail::allow_exceptions,boost::json::detail::no_context>  
1>        ]  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(96,20): message : see reference to function template instantiation 'std::variant<some_base_type<int>,int,const char *> boost::json::detail::value_to_impl<T,Context,boost::json::detail::supported_context<Ctx,T,boost::json::detail::value_to_conversion>>(boost::json::detail::full_context_conversion_tag,boost::json::value_to_tag<T>,const boost::json::value &,const Ctx &)' being compiled  
1>        with  
1>        [  
1>            T=bare_T,  
1>            Context=boost::json::detail::no_context,  
1>            Ctx=boost::json::detail::no_context  
1>        ]  
1>D:\loew\proj\ebsilon.git\build\SRC\Libs\boost\AnyCPU\boost\boost\json\value_to.hpp(148,12): message : see reference to function template instantiation 'T boost::json::value_to<std::variant<some_base_type<int>,int,const char *>,boost::json::detail::no_context>(const boost::json::value &,const Context &)' being compiled  
1>        with  
1>        [  
1>            T=std::variant<some_base_type<int>,int,const char *>,  
1>            Context=boost::json::detail::no_context  
1>        ]  
1>C:\Users\K8705963\source\repos\ConsoleApplication3\ConsoleApplication3\ConsoleApplication3.cpp(27,25): message : see reference to function template instantiation 'T boost::json::value_to<std::variant<some_base_type<int>,int,const char *>>(const boost::json::value &)' being compiled  
1>        with  
1>        [  
1>            T=std::variant<some_base_type<int>,int,const char *>  
1>        ]  
1>Done building project "ConsoleApplication3.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
========== Build started at 10:42 AM and took 02.027 seconds ==========  
```

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-08-28 09:58:03 UTC  
> Updated at: 2023-08-28 10:20:43 UTC  
> Url: https://github.com/boostorg/json/issues/934#issuecomment-1695398405  

Ok, I did pinpoint your problem. It affects **1.83.0** and _not_ 1.82.0.

---

## Comment 4

> Username: grisumbras  
> Created at: 2023-08-28 10:20:30 UTC  
> Updated at: 2023-08-28 10:45:40 UTC  
> Url: https://github.com/boostorg/json/issues/934#issuecomment-1695429396  

OK, so the source of your problem is that the library provides a higher ranked conversion for `std::variant` now. This is due to a new feature called "contextual conversions". In order to get your conversion to rank better you have to add extra parameters:  
  
```c++  
template< class T, class Ctx1, class Ctx2 >  
some_type<T>  
tag_invoke(  
    boost::json::value_to_tag< some_type<T> > const&,  
    boost::json::value const& jv,  
    Ctx1 const&,  
    Ctx2 const& ctx)  
{  
    // use value_to< X >( jv, ctx );  
    (void) ctx;  
    boost::json::string const* str = jv.if_string();  
    if (str && *str == "T11")  
        return some_type<T>{""};  
    throw std::invalid_argument("!");  
}  
```  
  
If you're interested what these parameters do, first context parameter is needed to provide "special" conversions. Here it isn't needed. Second context parameter is the full context  given by the caller. Composite types (like vector or variant) need to marshall it to conversions of elements.  
  
Just to be clear, if you don't need contexts in your program, you can ignore them. But the parameters (and template parameters) have to be present, so that your overload ranks high enough.

---

## Comment 5

> Username: tobias-loew  
> Created at: 2023-08-28 14:04:33 UTC  
> Updated at: 2023-08-28 14:05:13 UTC  
> Url: https://github.com/boostorg/json/issues/934#issuecomment-1695761158  

Thanks a lot for the clarification and sorry for messing up the version numbers.  
I also had to fix my `tag_invoke`s for `value_from`.   
Now everything works again.  
  
As an afterthough: With the new variant-support it would have been better to create a concept for the types in the variant, and then overload `tag_invoke` on that concept.

---

## Comment 6

> Username: grisumbras  
> Created at: 2023-08-28 14:08:36 UTC  
> Url: https://github.com/boostorg/json/issues/934#issuecomment-1695767933  

That will most likely happen in the next release, because another feature would benefit from it.
