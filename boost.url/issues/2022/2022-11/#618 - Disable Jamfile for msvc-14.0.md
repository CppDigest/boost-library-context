# #618 - Disable Jamfile for msvc-14.0 [Closed]

> Username: vinniefalco  
> Created at: 2022-11-05 17:37:52 UTC  
> Updated at: 2022-11-07 22:13:40 UTC  
> Closed at: 2022-11-07 22:13:40 UTC  
> Url: https://github.com/boostorg/url/issues/618  

You need to fix your Jamfile to disable the library for msvc-14.0, because  
your current checks do not exclude it. Add <toolset>msvc-14.0:<build>no  
either somewhere here  
  
https://github.com/boostorg/url/blob/643d959e3db8b59cf3f065ec1738cd0197c70b02/build/Jamfile#L11  
  
or here  
  
https://github.com/boostorg/url/blob/643d959e3db8b59cf3f065ec1738cd0197c70b02/Jamfile#L13  
  
  
C:\boost-git\develop\libs\url>b2 build toolset=msvc-14.0  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
    - symlinks supported       : yes (cached)  
    - cxx11_constexpr          : yes (cached) [2]  
    - cxx11_decltype           : yes (cached) [2]  
    - cxx11_hdr_tuple          : yes [2]  
    - cxx11_template_aliases   : yes [2]  
    - cxx11_variadic_templates : yes [2]  
    - cxx11_constexpr          : yes (cached) [3]  
    - cxx11_decltype           : yes (cached) [3]  
    - cxx11_hdr_tuple          : yes (cached) [3]  
    - cxx11_template_aliases   : yes (cached) [3]  
    - cxx11_variadic_templates : yes (cached) [3]  
  
[1] msvc-14.0  
[2] msvc-14.0/debug/python-2.7/threadapi-pthread/threading-multi/visibility-hidden  
[3] msvc-14.0/debug/python-2.7/threadapi-win32/threading-multi/visibility-hidden  
...patience...  
...found 1343 targets...  
...updating 9 targets...  
compile-c-c++ ..\..\bin.v2\libs\url\build\msvc-14.0\debug\threading-multi\src.obj  
src.cpp  
C:\boost-git\develop\boost/url/grammar/lut_chars.hpp(262): warning C4800: 'uint64_t': forcing value to bool 'true' or 'false' (performance warning)  
C:\boost-git\develop\boost/url/grammar/detail/tuple.hpp(83): warning C4814: 'boost::urls::grammar::detail::tuple_element_impl<I,T>::get': in C++14 'constexpr' will not imply 'const'; consider explicitly specifying 'const'  
C:\boost-git\develop\boost/url/grammar/detail/tuple.hpp(91): note: see reference to class template instantiation 'boost::urls::grammar::detail::tuple_element_impl<I,T>' being compiled  
C:\boost-git\develop\boost/url/param.hpp(886): error C2134: 'boost::urls::grammar::string_view_base::string_view_base': call does not result in a constant expression  
C:\boost-git\develop\boost/url/param.hpp(886): note: failure was caused by call of undefined function or one not declared 'constexpr'  
C:\boost-git\develop\boost/url/grammar/string_view_base.hpp(72): note: see declaration of 'boost::urls::grammar::string_view_base::string_view_base'  
C:\boost-git\develop\boost/url/rfc/pchars.hpp(45): error C2127: 'boost::urls::pchars': illegal initialization of 'constexpr' entity with a non-constant expression  
C:\boost-git\develop\boost/url/grammar/lut_chars.hpp(300): note: a non-constant (sub-)expression was encountered

---

## Comment 1

> Username: pdimov  
> Created at: 2022-11-07 13:30:03 UTC  
> Url: https://github.com/boostorg/url/issues/618#issuecomment-1305620084  

> Add msvc-14.0:no  
  
That's `<toolset>msvc-14.0:<build>no`.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-11-07 21:02:43 UTC  
> Url: https://github.com/boostorg/url/issues/618#issuecomment-1306187307  

Get this to the Greek!
