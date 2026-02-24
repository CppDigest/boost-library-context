# #160 - Boost.Log configuration with respect to BOOST_LOG_NO_CXX11_CODECVT_FACETS is not correct. [Closed]

> Username: Xerus2  
> Created at: 2021-08-19 15:40:14 UTC  
> Updated at: 2021-08-19 17:23:42 UTC  
> Closed at: 2021-08-19 17:23:42 UTC  
> Url: https://github.com/boostorg/log/issues/160  

It looks like Boost.Log bases its setting of the BOOST_LOG_NO_CXX11_CODECVT_FACETS macro entirely on Boost.Config's setting of the BOOST_NO_CXX11_HDR_CODECVT macro, here:  
https://github.com/boostorg/log/blob/develop/include/boost/log/detail/config.hpp#L101  
  
This is a big hammer, and contrary to the comment in the code there, a valid codecvt header is not required to use the features of std::codecvt, because it actually lives in the locale header:  
https://cplusplus.com/reference/locale/codecvt/  
  
This is important, because the codecvt header is deprecated starting in C++17, and thus, Boost.Config will continue to set the BOOST_NO_CXX11_HDR_CODECVT macro in that case. If you try to use the codecvt header on C++17, you will get a deprecation error. The locale header is *not* deprecated, however.  
  
I discussed this with the dev for Boost.Config: https://github.com/boostorg/config/issues/391  
It looks like they're going to fix the setting of BOOST_NO_CXX11_HDR_CODECVT for C++14 in Boost.Config, but since the macro is going to continue to be set in C++17, Boost.Log is still going to set BOOST_LOG_NO_CXX11_CODECVT_FACETS as a result, which it shouldn't, because it looks like Boost.Log is not using any of the deprecated features and setting BOOST_LOG_NO_CXX11_CODECVT_FACETS disables the overloads for automatic character conversions for logging char16_t and char32_t, which means that users have to define their own overloads for these operations on Windows (see what the macro controls in https://github.com/boostorg/log/blob/develop/src/code_conversion.cpp, https://github.com/boostorg/log/blob/develop/include/boost/log/detail/code_conversion.hpp, and https://github.com/boostorg/log/blob/develop/include/boost/log/utility/formatting_ostream.hpp.)   
  
This is complicated by the fact that MSVC >= 1900 and < 1922 have a bug which prevents linking against std::codecvt (see: https://stackoverflow.com/questions/32055357/visual-studio-c-2015-stdcodecvt-with-char16-t-or-char32-t), so that has to be taken into account when deciding whether to set BOOST_LOG_NO_CXX11_CODECVT_FACETS. It looks like a commit was done when the MSVC bug was found, as well: https://github.com/boostorg/log/commit/4751a165366dd9d1cf55252c290eaacf39a59552

---

## Comment 1

> Username: Lastique  
> Created at: 2021-08-19 17:23:42 UTC  
> Url: https://github.com/boostorg/log/issues/160#issuecomment-902102286  

The comment in `log/detail/config.hpp` explains that `<codecvt>` availability is used as a telltale sign for availability of `std::codecvt` specializations for `char16_t` and `char32_t`. This heuristic worked fine until recently. In any case, this should be fixed by #159.
