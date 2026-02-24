# #116 - MSVC + boost 1.70 compilation error when windows.h is already included (detail/thread_mutex.hpp) [Closed]

> Username: mrpi  
> Created at: 2019-04-17 19:21:54 UTC  
> Updated at: 2019-04-24 21:12:52 UTC  
> Closed at: 2019-04-24 21:12:32 UTC  
> Url: https://github.com/boostorg/container/issues/116  

There is an ADL problem with boost 1.70.0 that did not occur for us with 1.67.0.  
  
When _BOOST_USE_WINDOWS_H_ is not defined but  _windows.h_ has already been included, the calls to _InitializeCriticalSection(Ex)_, EnterCriticalSection, _LeaveCriticalSection_ and _DeleteCriticalSection_ are ambiguous.  
  
I have pasted the problematic part on godbold where you can see the bug:  
[Godbolt of bug](https://godbolt.org/z/jJ3OL_)  
If you uncomment the line `//#define WORKAROUND` a simple fix is applied.  
  
[file with error: detail/thread_mutex.hpp](https://github.com/boostorg/container/blob/develop/include/boost/container/detail/thread_mutex.hpp)  
  
> error C2668: 'boost::container::dtl::InitializeCriticalSection': ambiguous call to overloaded function  
> note: could be 'void boost::container::dtl::InitializeCriticalSection(_RTL_CRITICAL_SECTION *)'  
> C:/WinSdk/Include/10.0.17763.0/um\synchapi.h(123): note: or       'void InitializeCriticalSection(LPCRITICAL_SECTION)' [found using argument-dependent lookup]  
> note: while trying to match the argument list '(_RTL_CRITICAL_SECTION *)'  
>  
> error C2668: 'boost::container::dtl::EnterCriticalSection': ambiguous call to overloaded function  
> note: could be 'void boost::container::dtl::EnterCriticalSection(_RTL_CRITICAL_SECTION *)'  
> C:/WinSdk/Include/10.0.17763.0/um\synchapi.h(133): note: or       'void EnterCriticalSection(LPCRITICAL_SECTION)' [found using argument-dependent lookup]  
> note: while trying to match the argument list '(_RTL_CRITICAL_SECTION *)'  
>  
> error C2668: 'boost::container::dtl::LeaveCriticalSection': ambiguous call to overloaded function  
> note: could be 'void boost::container::dtl::LeaveCriticalSection(_RTL_CRITICAL_SECTION *)'  
> C:/WinSdk/Include/10.0.17763.0/um\synchapi.h(141): note: or       'void LeaveCriticalSection(LPCRITICAL_SECTION)' [found using argument-dependent lookup]  
> note: while trying to match the argument list '(_RTL_CRITICAL_SECTION *)'  
>  
> error C2668: 'boost::container::dtl::DeleteCriticalSection': ambiguous call to overloaded function  
> note: could be 'void boost::container::dtl::DeleteCriticalSection(_RTL_CRITICAL_SECTION *)'  
> C:/WinSdk/Include/10.0.17763.0/um\synchapi.h(194): note: or       'void DeleteCriticalSection(LPCRITICAL_SECTION)' [found using argument-dependent lookup]  
> note: while trying to match the argument list '(_RTL_CRITICAL_SECTION *)'

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-04-24 21:12:52 UTC  
> Url: https://github.com/boostorg/container/issues/116#issuecomment-486428541  

Thanks for the report!
