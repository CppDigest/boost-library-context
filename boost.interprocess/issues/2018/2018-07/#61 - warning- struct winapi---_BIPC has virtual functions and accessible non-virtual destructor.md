# #61 - warning: struct winapi::*_BIPC has virtual functions and accessible non-virtual destructor [Closed]

> Username: kivadiu  
> Created at: 2018-07-30 07:16:51 UTC  
> Updated at: 2018-09-18 19:28:57 UTC  
> Closed at: 2018-09-18 19:28:57 UTC  
> Url: https://github.com/boostorg/interprocess/issues/61  

This simple program:  
```  
#include <boost/interprocess/sync/file_lock.hpp>  
int main() {  
  return 0;  
}  
```  
cross-compiled from linux to Windows 64 bits with mingw-w64 + g++ 8.2.0 with the following command:  
`x86_64-w64-mingw32-g++-8.2.0 -o interprocess.o -c -Wnon-virtual-dtor -std=c++14 -I/softs/win64-mingw-8.2.0/release/boost/include interprocess.cpp`  
gives the following warnings:  
```  
In file included from /softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/errors.hpp:41,  
                 from /softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/exceptions.hpp:24,  
                 from /softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/sync/file_lock.hpp:24,  
                 from interprocess.cpp:1:  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:189:8: warning: 'struct boost::interprocess::winapi::IUnknown_BIPC' has virtual functions and accessible non-virtual destructor [-Wnon-virtual-dtor]  
 struct IUnknown_BIPC  
        ^~~~~~~~~~~~~  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:200:8: warning: base class 'struct boost::interprocess::winapi::IUnknown_BIPC' has accessible non-virtual destructor [-Wnon-virtual-dtor]  
 struct IWbemClassObject_BIPC : public IUnknown_BIPC  
        ^~~~~~~~~~~~~~~~~~~~~  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:200:8: warning: 'struct boost::interprocess::winapi::IWbemClassObject_BIPC' has virtual functions and accessible non-virtual destructor [-Wnon-virtual-dtor]  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:306:8: warning: base class 'struct boost::interprocess::winapi::IUnknown_BIPC' has accessible non-virtual destructor [-Wnon-virtual-dtor]  
 struct IWbemContext_BIPC : public IUnknown_BIPC  
        ^~~~~~~~~~~~~~~~~  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:306:8: warning: 'struct boost::interprocess::winapi::IWbemContext_BIPC' has virtual functions and accessible non-virtual destructor [-Wnon-virtual-dtor]  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:345:8: warning: base class 'struct boost::interprocess::winapi::IUnknown_BIPC' has accessible non-virtual destructor [-Wnon-virtual-dtor]  
 struct IEnumWbemClassObject_BIPC : public IUnknown_BIPC  
        ^~~~~~~~~~~~~~~~~~~~~~~~~  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:345:8: warning: 'struct boost::interprocess::winapi::IEnumWbemClassObject_BIPC' has virtual functions and accessible non-virtual destructor [-Wnon-virtual-dtor]  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:369:8: warning: base class 'struct boost::interprocess::winapi::IUnknown_BIPC' has accessible non-virtual destructor [-Wnon-virtual-dtor]  
 struct IWbemServices_BIPC : public IUnknown_BIPC  
        ^~~~~~~~~~~~~~~~~~  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:369:8: warning: 'struct boost::interprocess::winapi::IWbemServices_BIPC' has virtual functions and accessible non-virtual destructor [-Wnon-virtual-dtor]  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:518:8: warning: base class 'struct boost::interprocess::winapi::IUnknown_BIPC' has accessible non-virtual destructor [-Wnon-virtual-dtor]  
 struct IWbemLocator_BIPC : public IUnknown_BIPC  
        ^~~~~~~~~~~~~~~~~  
/softs/win64-mingw-8.2.0/release/boost/include/boost/interprocess/detail/win32_api.hpp:518:8: warning: 'struct boost::interprocess::winapi::IWbemLocator_BIPC' has virtual functions and accessible non-virtual destructor [-Wnon-virtual-dtor]  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-09-18 19:28:57 UTC  
> Url: https://github.com/boostorg/interprocess/issues/61#issuecomment-422518884  

The binary format of the class can't be modified so I've chosen to silence the warning. Many thanks for the report.
