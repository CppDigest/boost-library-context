# #10 - no Windows Embedded Compact 2013 support [Closed]

> Username: frederich  
> Created at: 2017-01-18 06:42:46 UTC  
> Updated at: 2017-04-22 09:53:09 UTC  
> Closed at: 2017-04-22 09:53:09 UTC  
> Url: https://github.com/boostorg/dll/issues/10  

The trivial example doesn't compile for Windows Embedded Compact 2013 SDK, normal Windows SDK works well.  
```  
#include <boost/dll.hpp>  
int wmain() {}  
```  
  
```  
1>  t.cpp  
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

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-01-18 19:28:39 UTC  
> Url: https://github.com/boostorg/dll/issues/10#issuecomment-273575444  

Looks like some issue within SDK.  
  
Could you please try the following code:  
```  
#include <windows.h> // this line was added  
#include <boost/dll.hpp>  
int wmain() {}  
```

---

## Comment 2

> Username: frederich  
> Created at: 2017-01-19 08:31:43 UTC  
> Url: https://github.com/boostorg/dll/issues/10#issuecomment-273711431  

Sure, result below.  
  
```  
1>  t.cpp  
1>D:\libraries\boost_1_63_0\boost/detail/winapi/dll.hpp(58): error C2375: 'LoadLibraryW' : redefinition; different linkage  
1>          C:\Program Files (x86)\Vector Vrtp\SDKs\WEC800_VRTP_SDK\sdk\Inc\winbase.h(1261) : see declaration of 'LoadLibraryW'  
1>D:\libraries\boost_1_63_0\boost/detail/winapi/dll.hpp(100): error C2375: 'VirtualQuery' : redefinition; different linkage  
1>          C:\Program Files (x86)\Vector Vrtp\SDKs\WEC800_VRTP_SDK\sdk\Inc\winbase.h(1013) : see declaration of 'VirtualQuery'  
```

---

## Comment 3

> Username: apolukhin  
> Created at: 2017-01-19 19:10:00 UTC  
> Url: https://github.com/boostorg/dll/issues/10#issuecomment-273868649  

@Lastique , could you help me out? Looks like some issue in Boost.WinAPI  
  
@frederich , could you please copy to this ticket the `LoadLibraryW` and the `VirtualQuery` definitions from SDKs\WEC800_VRTP_SDK\sdk\Inc\winbase.h ?

---

## Comment 4

> Username: Lastique  
> Created at: 2017-01-19 19:42:06 UTC  
> Url: https://github.com/boostorg/dll/issues/10#issuecomment-273877202  

> Looks like some issue in Boost.WinAPI  
  
Might be. I've never heard of Windows Embedded Compact SDK before, the closest thing that I've known is Windows CE SDK, which I think is dead nowdays. So Boost.WinAPI is definitely not adapted or tested for WECSDK.  
  
I doubt `LoadLibraryW` and `VirtualQuery` definitions alone will help, because most likely there are macros involved. To support WECSDK it would take someone to download the SDK and update all definitions in Boost.WinAPI accordingly. After a quick google I couldn't find if it is possible to download without registration, so that is going to be a problem.  
  
As a possible workaround I can suggest defining `BOOST_USE_WINDOWS_H`. That way all Boost should use `windows.h`, including Boost.WinAPI.

---

## Comment 5

> Username: frederich  
> Created at: 2017-01-20 07:15:16 UTC  
> Url: https://github.com/boostorg/dll/issues/10#issuecomment-273994045  

@Lastique, Windows Embedded Compact 2013 is Windows CE 8.0.  
  
No, Windows CE SDK isn't dead. There are not one Windows CE SDK. Windows CE is a real-time operation system building kit. You can select components, for instance ATL, COM, CMD Console, .Net Framework and compiler your special operating system and on the end generate the SDK for it.

---

## Comment 6

> Username: frederich  
> Created at: 2017-01-20 07:18:19 UTC  
> Updated at: 2017-01-20 07:21:32 UTC  
> Url: https://github.com/boostorg/dll/issues/10#issuecomment-273994459  

the defintions from SDKs\WEC800_VRTP_SDK\sdk\Inc\winbase.h  
```  
HINSTANCE  
WINAPI  
LoadLibraryW(  
    LPCWSTR lpLibFileName  
    );  
#ifdef UNICODE  
#define LoadLibrary  LoadLibraryW  
#else  
#define LoadLibrary  LoadLibraryA  
#endif // !UNICODE  
```  
```  
DWORD  
WINAPI  
VirtualQuery(  
    LPCVOID lpAddress,  
    PMEMORY_BASIC_INFORMATION lpBuffer,  
    DWORD dwLength  
    );  
  
```

---

## Comment 7

> Username: frederich  
> Created at: 2017-01-20 07:19:46 UTC  
> Updated at: 2017-01-20 07:20:26 UTC  
> Url: https://github.com/boostorg/dll/issues/10#issuecomment-273994646  

results of using `BOOST_USE_WINDOWS_H`  
```  
#define BOOST_USE_WINDOWS_H  
#include <windows.h>  
#include <boost/dll.hpp>  
int wmain() {}  
```  
  
```  
>  t.cpp  
1>D:\libraries\boost_1_63_0\boost/dll/detail/aggressive_ptr_cast.hpp(52): error C2039: 'uintptr_t' : is not a member of 'boost'  
1>          D:\libraries\boost_1_63_0\boost/dll/detail/windows/shared_library_impl.hpp(152) : see reference to function template instantiation 'void *boost::dll::detail::aggressive_ptr_cast<void*,int(__cdecl *)(void)>(From)' being compiled  
1>          with  
1>          [  
1>              From=int (__cdecl *)(void)  
1>          ]  
```

---

## Comment 8

> Username: apolukhin  
> Created at: 2017-01-20 19:40:33 UTC  
> Url: https://github.com/boostorg/dll/issues/10#issuecomment-274161190  

@frederich please try to replace your boost_1_63_0\boost/dll/detail/aggressive_ptr_cast.hpp with [aggressive_ptr_cast.hpp with a fix](https://github.com/boostorg/dll/blob/529ff731ba851d8e6c272793d9a67ae6b3c502c3/include/boost/dll/detail/aggressive_ptr_cast.hpp) and compile with `#define BOOST_USE_WINDOWS_H`  
  
Also, could you please tell us, what is the value of `WINAPI` macro under WinCE 2013?

---

## Comment 9

> Username: frederich  
> Created at: 2017-01-23 06:16:02 UTC  
> Url: https://github.com/boostorg/dll/issues/10#issuecomment-274408736  

@apolukhin, thanks. Now it compiles with `#define BOOST_USE_WINDOWS_H`.   
  
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

---

## Comment 10

> Username: apolukhin  
> Created at: 2017-01-27 19:10:06 UTC  
> Updated at: 2017-01-27 19:11:23 UTC  
> Url: https://github.com/boostorg/dll/issues/10#issuecomment-275747910  

@frederich does the `BOOST_DLL_ALIAS_SECTIONED` work well?  
  
```  
#include <boost/dll/alias.hpp>  
namespace foo {  
    int bar(int v) {  
        return v;  
    }  
}  
  
BOOST_DLL_ALIAS_SECTIONED(foo::bar, foo_bar, "test")  
  
int main(){}  
```

---

## Comment 11

> Username: frederich  
> Created at: 2017-01-30 09:59:42 UTC  
> Url: https://github.com/boostorg/dll/issues/10#issuecomment-276021670  

@apolukhin, yes it compiles. Should I do any further checks?  
  
The substituted code:  
```  
#line 264 "D:\\libraries\\boost_1_63_0\\boost/dll/alias.hpp"  
  
#line 11 "t.cpp"  
namespace foo {  
    int bar(int v) {  
        return v;  
    }  
}  
  
namespace _autoaliases { extern "C" __declspec(dllexport) const void *foo_bar; static_assert(sizeof("boostdll") < 10, "Some platforms require section names to be at most 8 bytest"); __pragma(section("boostdll", read)) __declspec(allocate("boostdll")) __declspec(selectany) const void * foo_bar = reinterpret_cast<const void*>(reinterpret_cast<intptr_t>( &foo::bar )); }  
  
int wmain(){}  
```

---

## Comment 12

> Username: frederich  
> Created at: 2017-01-30 10:23:03 UTC  
> Url: https://github.com/boostorg/dll/issues/10#issuecomment-276026430  

@apolukhin boost.dll requires boost.system which is not header only. Do you have general Windows CE build instructions or at least the correct toolset; `--toolset=msvc-14.0` is wrong?

---

## Comment 13

> Username: apolukhin  
> Created at: 2017-02-06 20:29:37 UTC  
> Url: https://github.com/boostorg/dll/issues/10#issuecomment-277803631  

Not sure. Take a look at [this link](https://msdn.microsoft.com/en-us/library/jj919256.aspx)  
blddemo.bat may contain additional flags that must be passed to b2 as cxxflags="some flags" or linkflags="some flags".  
  
If that does not help - try asking for help in https://github.com/boostorg/build

---

## Comment 14

> Username: apolukhin  
> Created at: 2017-02-19 15:19:56 UTC  
> Url: https://github.com/boostorg/dll/issues/10#issuecomment-280925672  

Added two issues in related modules:  
* https://github.com/boostorg/winapi/issues/38  
* https://svn.boost.org/trac/boost/ticket/12853  
  
How's your progress with building Filesystem and System for WinCE?

---

## Comment 15

> Username: frederich  
> Created at: 2017-02-20 06:42:10 UTC  
> Updated at: 2017-02-20 06:42:55 UTC  
> Url: https://github.com/boostorg/dll/issues/10#issuecomment-281000120  

Thank you for creating the tickets!   
  
Up to now, there is no progress. I was busy with other stuff. I'll schedule it for this week.

---

## Comment 16

> Username: apolukhin  
> Created at: 2017-04-22 09:53:09 UTC  
> Url: https://github.com/boostorg/dll/issues/10#issuecomment-296362017  

Looks like all the Boost.DLL related issues were fixed.
