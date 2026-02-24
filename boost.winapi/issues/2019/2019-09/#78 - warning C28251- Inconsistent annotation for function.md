# #78 - warning C28251: Inconsistent annotation for function [Closed]

> Username: Riadela  
> Created at: 2019-09-11 09:31:51 UTC  
> Updated at: 2020-05-02 16:45:17 UTC  
> Closed at: 2020-05-02 16:45:17 UTC  
> Url: https://github.com/boostorg/winapi/issues/78  

When I'm building my solution in release mode I keep getting this warning which is treated as an error.  
Specifically it happens in some files in the winapi folder, such as get_last_error.hpp, on the GetLastError function  
  
I dont want to use pragma disable warning on my projects, does anyone have any idea of why this happens..?

---

## Comment 1

> Username: Lastique  
> Created at: 2019-09-11 13:48:44 UTC  
> Url: https://github.com/boostorg/winapi/issues/78#issuecomment-530388902  

1. What Windows SDK and compiler are you using?  
2. Can you identify the difference between Boost.WinAPI and Windows SDK declarations?  
  
As a workaround you can define `BOOST_USE_WINDOWS_H` when building your code to force Boost.WinAPI use `windows.h` instead of declaring functions and types on its own.

---

## Comment 2

> Username: Riadela  
> Created at: 2019-09-12 08:17:51 UTC  
> Url: https://github.com/boostorg/winapi/issues/78#issuecomment-530718797  

I'm on SDK 10.0.17134 and using VS17 (msvc 141)  
Yeah I can list a couple of them that I get as an error  
get_last_error.hpp  
`BOOST_SYMBOL_IMPORT boost::winapi::DWORD_ BOOST_WINAPI_WINAPI_CC GetLastError(BOOST_WINAPI_DETAIL_VOID);`  
  
errhandling.h   (from my windows kits)  
  
```  
#ifndef _M_CEE_PURE  
  
WINBASEAPI _Check_return_   
_Post_equals_last_error_  
DWORD  
WINAPI  
GetLastError(  
    VOID  
    );  
#endif  
```  
Or in boost again in error_handling.hpp  
```  
BOOST_SYMBOL_IMPORT boost::winapi::DWORD_ BOOST_WINAPI_WINAPI_CC  
FormatMessageA(  
    boost::winapi::DWORD_ dwFlags,  
    boost::winapi::LPCVOID_ lpSource,  
    boost::winapi::DWORD_ dwMessageId,  
    boost::winapi::DWORD_ dwLanguageId,  
    boost::winapi::LPSTR_ lpBuffer,  
    boost::winapi::DWORD_ nSize,  
    va_list *Arguments);  
```  
And in my sdk in WinBase.h  
```  
  
#if !defined(MIDL_PASS)  
WINBASEAPI  
_Success_(return != 0)  
DWORD  
WINAPI  
FormatMessageA(  
    _In_     DWORD dwFlags,  
    _In_opt_ LPCVOID lpSource,  
    _In_     DWORD dwMessageId,  
    _In_     DWORD dwLanguageId,  
    _When_((dwFlags & FORMAT_MESSAGE_ALLOCATE_BUFFER) != 0, _At_((LPSTR*)lpBuffer, _Outptr_result_z_))  
    _When_((dwFlags & FORMAT_MESSAGE_ALLOCATE_BUFFER) == 0, _Out_writes_z_(nSize))  
             LPSTR lpBuffer,  
    _In_     DWORD nSize,  
    _In_opt_ va_list *Arguments  
    );  
```  
  
> 2>c:\thirdparty\source\boost\boost_1_71_0\boost\winapi\error_handling.hpp(25): error C28251: Inconsistent annotation for 'FormatMessageA': this instance has no annotations. See c:\program files (x86)\windows kits\10\include\10.0.17134.0\um\winbase.h(2387).

---

## Comment 3

> Username: Riadela  
> Created at: 2019-09-12 08:21:09 UTC  
> Url: https://github.com/boostorg/winapi/issues/78#issuecomment-530719921  

I also get this unusual warning .. which it seems I'm the only one in the web   
  
> c:\\thirdparty\source\boost\boost_1_71_0\boost\math\special_functions\next.hpp(502): error C6031: Return value ignored: 'frexp'.

---

## Comment 4

> Username: Lastique  
> Created at: 2019-09-12 08:34:10 UTC  
> Url: https://github.com/boostorg/winapi/issues/78#issuecomment-530724626  

The math one is not related to Boost.WinAPI. You can report it to [Boost.Math](https://github.com/boostorg/math/issues/new).  
  
The other warnings seem to be caused by returned value and argument check attributes. I won't be adding those because different Windows SDKs use different markup for those. I recommend you disabling the warning. I'll probably disable it in the future with a pragma.
