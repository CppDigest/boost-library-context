# #102 - Compile error on wince [Closed]

> Username: Arenoros  
> Created at: 2019-10-06 19:23:07 UTC  
> Updated at: 2020-02-29 14:06:54 UTC  
> Closed at: 2020-02-29 14:06:54 UTC  
> Url: https://github.com/boostorg/predef/issues/102  

_boost\predef\platform\windows_uwp.h_   
WinCE has no ntverp.h header   
fix:  
  
\- #if !defined(\_\_MINGW32__)  
\+ #if !defined(\_\_MINGW32__) && **!defined(_WIN32_WCE)**
