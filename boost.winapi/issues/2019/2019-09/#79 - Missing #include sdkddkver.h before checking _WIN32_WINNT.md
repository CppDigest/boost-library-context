# #79 - Missing #include sdkddkver.h before checking _WIN32_WINNT [Closed]

> Username: k15tfu  
> Created at: 2019-09-12 19:23:15 UTC  
> Updated at: 2019-09-12 22:20:07 UTC  
> Closed at: 2019-09-12 22:20:07 UTC  
> Url: https://github.com/boostorg/winapi/issues/79  

Hi!  
  
I'm defining _WIN32_WINNT to _WIN32_WINNT_VISTA in my project, but then I get the following error from boost:  
```  
.../boost/process/detail/windows/locale.hpp(81): error C2039: 'WC_NO_BEST_FIT_CHARS_': is not a member of 'boost::winapi'  
```  
  
I found that this constant should be defined here:  
```  
#if BOOST_USE_WINAPI_VERSION >= BOOST_WINAPI_VERSION_WIN2K  
BOOST_CONSTEXPR_OR_CONST DWORD_ WC_NO_BEST_FIT_CHARS_ = 0x00000400;  
#endif  
```  
  
but unfortunately it evaluates to false because _WIN32_WINNT_VISTA is not defined at this point. Adding #include sdkddkver.h before comparing _WIN32_WINNT macro fixes this problem.  
  
I think the right place for this is boost/winapi/config.hpp because that's where BOOST_USE_WINAPI_VERSION is defined.

---

## Comment 1

> Username: Lastique  
> Created at: 2019-09-12 22:20:07 UTC  
> Url: https://github.com/boostorg/winapi/issues/79#issuecomment-531028930  

AFAIR, `sdkddkver.h` is not universally available, so we can't include it.  
  
In any case, defining macros like `_WIN32_WINNT` in command line to non-numeric values is incorrect exactly for the reason you outlined - the code may check the macros without including any headers and because macros like `_WIN32_WINNT_VISTA` are not defined such checks will fail. You should define `_WIN32_WINNT=0x0600` in the command line.
