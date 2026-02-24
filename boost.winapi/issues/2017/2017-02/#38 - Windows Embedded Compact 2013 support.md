# #38 - Windows Embedded Compact 2013 support [Closed]

> Username: apolukhin  
> Created at: 2017-02-19 14:56:59 UTC  
> Updated at: 2017-09-16 18:10:17 UTC  
> Closed at: 2017-09-16 18:10:17 UTC  
> Url: https://github.com/boostorg/winapi/issues/38  

In https://github.com/boostorg/dll/issues/10 it was discovered that Windows Embedded Compact 2013 is currently not supported. That leads to errors like:  
  
```  
1>C:\Program Files (x86)\Vector Vrtp\SDKs\WEC800_VRTP_SDK\sdk\Inc\kfuncs.h(415): error C2061: syntax error : identifier 'LPVOID'  
1>C:\Program Files (x86)\Vector Vrtp\SDKs\WEC800_VRTP_SDK\sdk\Inc\kfuncs.h(563): error C2146: syntax error : missing ';' before identifier 'LockResource'  
1>C:\Program Files (x86)\Vector Vrtp\SDKs\WEC800_VRTP_SDK\sdk\Inc\kfuncs.h(563): error C2433: 'LPVOID' : 'inline' not permitted on data declarations  
1>C:\Program Files (x86)\Vector Vrtp\SDKs\WEC800_VRTP_SDK\sdk\Inc\kfuncs.h(563): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
1>C:\Program Files (x86)\Vector Vrtp\SDKs\WEC800_VRTP_SDK\sdk\Inc\kfuncs.h(563): error C2065: 'HGLOBAL' : undeclared identifier  
1>C:\Program Files (x86)\Vector Vrtp\SDKs\WEC800_VRTP_SDK\sdk\Inc\kfuncs.h(563): error C2146: syntax error : missing ')' before identifier 'hResData'  
1>C:\Program Files (x86)\Vector Vrtp\SDKs\WEC800_VRTP_SDK\sdk\Inc\kfuncs.h(563): error C2059: syntax error : ')'  
1>C:\Program Files (x86)\Vector Vrtp\SDKs\WEC800_VRTP_SDK\sdk\Inc\kfuncs.h(563): error C2143: syntax error : missing ';' before '{'  
1>C:\Program Files (x86)\Vector Vrtp\SDKs\WEC800_VRTP_SDK\sdk\Inc\kfuncs.h(563): error C2447: '{' : missing function header (old-style formal list?)  
1>C:\Program Files (x86)\Vector Vrtp\SDKs\WEC800_VRTP_SDK\sdk\Inc\kfuncs.h(624): error C2061: syntax error : identifier 'LPDWORD'  
1>C:\Program Files (x86)\Vector Vrtp\SDKs\WEC800_VRTP_SDK\sdk\Inc\kfuncs.h(645): error C2061: syntax error : identifier 'LPDWORD'  
1>C:\Program Files (x86)\Vector Vrtp\SDKs\WEC800_VRTP_SDK\sdk\Inc\kfuncs.h(656): error C2061: syntax error : identifier 'LPDWORD'  
```  
  
Inclusion of `#include <windows.h>` leads to errors with linkage:  
```  
1>D:\libraries\boost_1_63_0\boost/detail/winapi/dll.hpp(58): error C2375: 'LoadLibraryW' : redefinition; different linkage  
1>          C:\Program Files (x86)\Vector Vrtp\SDKs\WEC800_VRTP_SDK\sdk\Inc\winbase.h(1261) : see declaration of 'LoadLibraryW'  
1>D:\libraries\boost_1_63_0\boost/detail/winapi/dll.hpp(100): error C2375: 'VirtualQuery' : redefinition; different linkage  
1>          C:\Program Files (x86)\Vector Vrtp\SDKs\WEC800_VRTP_SDK\sdk\Inc\winbase.h(1013) : see declaration of 'VirtualQuery'  
```  
  
`WINAPI` is defined in the following way:  
```  
#elif (_MSC_VER >= 800) || defined(_STDCALL_SUPPORTED)  
#define CALLBACK    __stdcall  
#define WINAPI      __stdcall  
#define WINAPIV     __cdecl  
#define APIENTRY    WINAPI  
#define APIPRIVATE  __stdcall  
#define PASCAL      __stdcall  
#else  
```  
  
Defining `BOOST_USE_WINDOWS_H` helps to workaround all the issues.

---

## Comment 1

> Username: Lastique  
> Created at: 2017-02-20 15:01:39 UTC  
> Url: https://github.com/boostorg/winapi/issues/38#issuecomment-281101265  

Unless you can provide a patch, I don't think I'll be able to fix it.  
  
Supporting WEC would require me to download and install the SDK, and then port all definitions in Boost.WinAPI to it. This is currently too much time for me to spend.  
  
I'm going to close this ticket for now and flag WEC as unsupported beyond the `BOOST_USE_WINDOWS_H` mode. If I have time in the future I might end up doing a proper port.

---

## Comment 2

> Username: frederich  
> Created at: 2017-02-20 15:20:11 UTC  
> Url: https://github.com/boostorg/winapi/issues/38#issuecomment-281105964  

@Lastique, don't close the ticket, I'll provide a patch.

---

## Comment 3

> Username: Lastique  
> Created at: 2017-06-24 14:52:14 UTC  
> Url: https://github.com/boostorg/winapi/issues/38#issuecomment-310843038  

@frederich, any news on the patch?

---

## Comment 4

> Username: Lastique  
> Created at: 2017-09-16 18:10:17 UTC  
> Url: https://github.com/boostorg/winapi/issues/38#issuecomment-329985554  

No activity. Please, prepare a PR if the support is still needed.
