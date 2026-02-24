# #97 - mp_count.hpp Windows compile error [Closed]

> Username: tjcw  
> Created at: 2024-10-21 12:30:35 UTC  
> Updated at: 2024-10-22 16:42:50 UTC  
> Closed at: 2024-10-22 16:42:47 UTC  
> Url: https://github.com/boostorg/mp11/issues/97  

With this code fragment  
```  
#pragma GCC diagnostic push  
#pragma GCC diagnostic ignored "-Wnon-virtual-dtor"  
#include <boost/url/ipv6_address.hpp>  
#pragma GCC diagnostic pop  
```  
I get a compile error with the latest Microsoft Windows c++ compiler. My compile line and the error I get looks like  
```  
C:\PROGRA~1\MICROS~2\2022\PROFES~1\VC\Tools\MSVC\1440~1.338\bin\Hostx64\x64\cl.exe  /nologo /TP -DBOOST_ALL_DYN_LINK -DBOOST_ALL_NO_LIB -DBOOST_URL_DYN_LINK -DBOOST_URL_NO_LIB -DIMB_LOG_CURRENT_TARGET=evhtp -DU_DEFINE_FALSE_AND_TRUE=1 -DU_USING_ICU_NAMESPACE=1 -DYAML_CPP_DLL -Devhtp_EXPORTS -IC:\ci\product-build\WMB\src\prereq\mq\amd64_nt_4\inc -I"C:\Program Files\IBM\Java80\include" -external:W0 /DWIN32 /D_WINDOWS  /c /analyze /analyze:stacksize131072 /analyze:WX- /Zi /Ob0 /Od  -std:c++17 -MDd /DBOOST_CONFIG_SUPPRESS_OUTDATED_MESSAGE /MP /D_CRT_SECURE_NO_WARNINGS /we28182 /we28199 /we6011 /we6101 /we6201 /we6386 /we6262 /wd4251 /W3 /EHs /GR /DWIN32 /DWIN64 /D_WINDOWS /DWIN32_LEAN_AND_MEAN /DNOMINMAX /D_WIN32_WINNT=0x0603 /DWINVER=0x0603 /D_SILENCE_ALL_CXX17_DEPRECATION_WARNINGS /WX /wd4101 /wd4275 /wd4521 /wd4250 /wd4068 /wd4244 /wd4267 /wd4786 /wd4800 /we4996 /D_CRT_NONSTDC_NO_WARNINGS /D_WINSOCK_DEPRECATED_NO_WARNINGS /FS /Zi /DRELOCATABLE_BUILD /DUNLICENSED_BUILD /DMQSI_64BIT_BUILD /DMQSI_LITTLE_ENDIAN /WL /D_CRT_SECURE_NO_DEPRECATE /DXERCES_LONG_SRCOFFSET /DDECLITEND=1 /DLITTLE_ENDIAN /bigobj /Od /D_DEBUG /DMQSI_DEBUG_BUILD /GS /RTC1 -DEXPORT -DNO_SYS_UN -DEVHTP_SYS_ARCH=64 -DEVHTP_DLL_EXPORT -DNO_STRNDUP "-Dssize_t=long long" /showIncludes /FoWebServices\libevhtp\CMakeFiles\evhtp.dir\internal.cpp.obj /FdWebServices\libevhtp\CMakeFiles\evhtp.dir\ /FS -c C:\ci\product-build\WMB\src\WebServices\libevhtp\internal.cpp  
C:\ci\product-build\WMB\obj\amd64_nt_4\boost-1.86.0\include\boost\mp11\detail\mp_count.hpp(54) : warning C6294: Ill-defined for-loop.  Loop body not executed.  
C:\ci\product-build\WMB\obj\amd64_nt_4\boost-1.86.0\include\boost\mp11\detail\mp_count.hpp(56) : error C6201: Index '1' is out of valid index range '0' to '0' for possibly stack allocated buffer 'a'.  
  
```  
  
I am attempting a fix by changing lines in mp_count.hpp to look like  
```  
    if constexpr (sizeof...(T) > 0) {  
      for( std::size_t i = 1; i < sizeof...(T) + 1; ++i )  
      {  
          r += a[ i ];  
      }  
    }  
```  
but my build has not yet completed so I do not yet know if this fix works.

---

## Comment 1

> Username: tjcw  
> Created at: 2024-10-21 12:36:05 UTC  
> Url: https://github.com/boostorg/mp11/issues/97#issuecomment-2426549549  

It may be necessary to add code to the test case to exercise a function; I have  
```  
extern "C" int htp__is_ipv6_address(const char* candidate) {  
  boost::system::result<boost::urls::ipv6_address> res=boost::urls::parse_ipv6_address(candidate) ;  
  if ( res ) {  
    return 1 ;  
  } else {  
    return 0 ;  
  }  
}  
```  
in order to trip the error

---

## Comment 2

> Username: pdimov  
> Created at: 2024-10-21 14:28:46 UTC  
> Url: https://github.com/boostorg/mp11/issues/97#issuecomment-2426851031  

Looks like a bug in `/analyze`.  
  
Note that `if constexpr` requires C++17 so it will fail on every C++11 and C++14 compiler.  
  
You may try  
  
```  
      for( std::size_t i = 0; i < sizeof...(T); ++i )  
      {  
          r += a[ i+1 ];  
      }  
```

---

## Comment 3

> Username: tjcw  
> Created at: 2024-10-21 15:36:44 UTC  
> Url: https://github.com/boostorg/mp11/issues/97#issuecomment-2427036265  

My attempt with `if constexpr` didn't make any difference to the error message. I will try pdimov's suggestion.   
Have I found a defect in Microsoft's C++ compiler ?

---

## Comment 4

> Username: tjcw  
> Created at: 2024-10-22 08:03:54 UTC  
> Url: https://github.com/boostorg/mp11/issues/97#issuecomment-2428548475  

pdimov's fix resolved the issue. I have submitted a pull request.

---

## Comment 5

> Username: tjcw  
> Created at: 2024-10-22 12:50:44 UTC  
> Url: https://github.com/boostorg/mp11/issues/97#issuecomment-2429199121  

[boost_test_mp11.txt](https://github.com/user-attachments/files/17476411/boost_test_mp11.txt)  
The regression test for mp11 passes (on RHEL8 with   
```  
tjcw:~$ type g++  
g++ is /opt/rh/gcc-toolset-10/root/usr/bin/g++  
tjcw:~$  
```  
) with this change applied

---

## Comment 6

> Username: pdimov  
> Created at: 2024-10-22 16:42:48 UTC  
> Url: https://github.com/boostorg/mp11/issues/97#issuecomment-2429767693  

https://github.com/boostorg/mp11/pull/98 merged.
