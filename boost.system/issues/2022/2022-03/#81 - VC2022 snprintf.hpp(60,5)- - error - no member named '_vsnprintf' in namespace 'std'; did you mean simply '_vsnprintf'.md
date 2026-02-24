# #81 - VC2022 snprintf.hpp(60,5): : error : no member named '_vsnprintf' in namespace 'std'; did you mean simply '_vsnprintf'? [Closed]

> Username: olejniczak  
> Created at: 2022-03-24 21:22:34 UTC  
> Updated at: 2022-04-16 12:21:02 UTC  
> Closed at: 2022-04-16 09:55:41 UTC  
> Url: https://github.com/boostorg/system/issues/81  

I have this error in snprintf.hpp(60,5) using boost-locale 1.78 with MSVC 2022 (_MSC_VER == 1931)  
  
Probably the macro in line 26 should look like this:  
  
#if ( defined(_MSC_VER) && (_MSC_VER < 1900 **|| _MSC_VER > 1930**) ) || ( defined(__MINGW32__) && !defined(__MINGW64_VERSION_MAJOR) )

---

## Comment 1

> Username: Flamefire  
> Created at: 2022-04-16 09:55:41 UTC  
> Url: https://github.com/boostorg/system/issues/81#issuecomment-1100651918  

@olejniczak Please provide an example on how to reproduce this and a complete error (including the trace on how that error occured)  
  
Also note that this is not related to Boost.Locale at all but to Boost.System (@glenfe  @pdimov please move) judging by the mentioned line 26: https://github.com/boostorg/system/blob/boost-1.78.0/include/boost/system/detail/snprintf.hpp#L26  
  
It also seems to be an invalid issue: I fail to see a valid use that can lead to "no member named '_vsnprintf' in namespace 'std'" at https://github.com/boostorg/system/blob/boost-1.78.0/include/boost/system/detail/snprintf.hpp#L60  
  
I would assume that somewhere there is a `#define vsnprintf _vsnprintf` in the user code which should be removed. Hence closing this. Please reopen if that is not the case.
