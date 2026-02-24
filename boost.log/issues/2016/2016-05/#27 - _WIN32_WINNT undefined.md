# #27 - _WIN32_WINNT undefined [Closed]

> Username: chrullrich  
> Created at: 2016-05-04 21:39:59 UTC  
> Updated at: 2016-05-05 08:07:00 UTC  
> Closed at: 2016-05-05 08:07:00 UTC  
> Url: https://github.com/boostorg/log/issues/27  

While building Boost 1.60.0 (with VS 2015), I get this message for syslog_backend.cpp and init_from_settings.cpp:  
  
```  
Please define _WIN32_WINNT or _WIN32_WINDOWS appropriately. For example:  
- add -D_WIN32_WINNT=0x0501 to the compiler command line; or  
- add _WIN32_WINNT=0x0501 to your project's Preprocessor Definitions.  
Assuming _WIN32_WINNT=0x0501 (i.e. Windows XP target).  
```  
  
It comes from asio/detail/config.hpp, but is caused, I think, by detail/winapi/config.hpp getting its definitions wrong:  
  
```  
#if !defined(BOOST_USE_WINAPI_VERSION)  
#if defined(_WIN32_WINNT)  
#define BOOST_USE_WINAPI_VERSION _WIN32_WINNT  
#elif defined(WINVER)  
#define BOOST_USE_WINAPI_VERSION WINVER  
#else  
// By default use Windows Vista API on compilers that support it and XP on the others  
#if (defined(_MSC_VER) && _MSC_VER <= 1400) || defined(BOOST_WINAPI_IS_MINGW)  
#define BOOST_USE_WINAPI_VERSION BOOST_WINAPI_VERSION_WINXP  
#else  
#define BOOST_USE_WINAPI_VERSION BOOST_WINAPI_VERSION_WIN6  
#endif  
#endif  
#endif  
  
#if defined(BOOST_USE_WINDOWS_H)  
// We have to define the version macros so that windows.h provides the necessary symbols  
#if !defined(_WIN32_WINNT)  
#define _WIN32_WINNT BOOST_USE_WINAPI_VERSION  
#endif  
#if !defined(WINVER)  
#define WINVER BOOST_USE_WINAPI_VERSION  
#endif  
#endif  
  
```  
  
Note that the value of BOOST_USE_WINAPI_VERSION, defined or defaulted, is transferred to _WIN32_WINNT only if BOOST_USE_WINDOWS_H is defined. It is not defined in my build, and AFAICT I'm not doing anything that would remove it or prevent it from getting defined by whatever might be supposed to define it.

---

## Comment 1

> Username: Lastique  
> Created at: 2016-05-05 08:07:00 UTC  
> Url: https://github.com/boostorg/log/issues/27#issuecomment-217098418  

In the current Boost.Log `BOOST_USE_WINDOWS_H` is defined when building the library.
