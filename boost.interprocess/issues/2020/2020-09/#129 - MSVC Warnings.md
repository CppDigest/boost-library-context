# #129 - MSVC Warnings [Closed]

> Username: fsmoke  
> Created at: 2020-09-16 14:47:25 UTC  
> Updated at: 2021-02-01 14:11:29 UTC  
> Closed at: 2021-02-01 14:11:29 UTC  
> Url: https://github.com/boostorg/interprocess/issues/129  

I've got warnings. BOOST_USE_WINDOWS_H defined  
  
6>D:\libraries\boost_1_74_0\boost\interprocess\detail\win32_api.hpp(1030,60): warning C4191: 'reinterpret_cast': unsafe conversion from 'boost::interprocess::winapi::farproc_t' to 'boost::interprocess::winapi::NtQuerySystemInformation_t'  
6>D:\libraries\boost_1_74_0\boost\interprocess\detail\win32_api.hpp(1030,60): warning C4191: 'reinterpret_cast': unsafe conversion from 'boost::interprocess::winapi::farproc_t' to 'boost::interprocess::winapi::NtQuerySystemInformation_t'  
6>D:\libraries\boost_1_74_0\boost\interprocess\streams\bufferstream.hpp(135,1): warning C4242: '=': conversion from 'int' to '_Elem', possible loss of data  
6>D:\libraries\boost_1_74_0\boost\interprocess\streams\bufferstream.hpp(135,1): warning C4242: '=': conversion from 'int' to '_Elem', possible loss of data  
6>D:\libraries\boost_1_74_0\boost\interprocess\detail\win32_api.hpp(1030,60): warning C4191: 'reinterpret_cast': unsafe conversion from 'boost::interprocess::winapi::farproc_t' to 'boost::interprocess::winapi::NtQuerySystemInformation_t'  
6>D:\libraries\boost_1_74_0\boost\interprocess\streams\bufferstream.hpp(135,1): warning C4242: '=': conversion from 'int' to '_Elem', possible loss of data  
6>D:\libraries\boost_1_74_0\boost\interprocess\detail\win32_api.hpp(1030,60): warning C4191: 'reinterpret_cast': unsafe conversion from 'boost::interprocess::winapi::farproc_t' to 'boost::interprocess::winapi::NtQuerySystemInformation_t'  
6>D:\libraries\boost_1_74_0\boost\interprocess\streams\bufferstream.hpp(135,1): warning C4242: '=': conversion from 'int' to '_Elem', possible loss of data  
6>D:\libraries\boost_1_74_0\boost\interprocess\detail\win32_api.hpp(1030,60): warning C4191: 'reinterpret_cast': unsafe conversion from 'boost::interprocess::winapi::farproc_t' to 'boost::interprocess::winapi::NtQuerySystemInformation_t'  
6>D:\libraries\boost_1_74_0\boost\interprocess\detail\win32_api.hpp(1030,60): warning C4191: 'reinterpret_cast': unsafe conversion from 'boost::interprocess::winapi::farproc_t' to 'boost::interprocess::winapi::NtQuerySystemInformation_t'  
6>D:\libraries\boost_1_74_0\boost\interprocess\streams\bufferstream.hpp(135,1): warning C4242: '=': conversion from 'int' to '_Elem', possible loss of data  
6>D:\libraries\boost_1_74_0\boost\interprocess\streams\bufferstream.hpp(135,1): warning C4242: '=': conversion from 'int' to '_Elem', possible loss of data  
6>    12 Warning(s)

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-09-22 12:33:58 UTC  
> Url: https://github.com/boostorg/interprocess/issues/129#issuecomment-696691514  

I can't reproduce those warnings with Visual 2019 in both x64 and x86 with W4 warning level. Which MSVC version and configuration are you using?  
  
This is the test case I'm using with 0 warnings (Level /W4):  
  
```  
#define BOOST_USE_WINDOWS_H  
#include <boost/interprocess/streams/bufferstream.hpp>  
#include <boost/interprocess/detail/win32_api.hpp>  
  
int main ()  
{  
   return 0;  
}  
  
```  
  
Can you send me a test case that outputs those warnings?

---

## Comment 2

> Username: igaztanaga  
> Created at: 2021-02-01 14:11:29 UTC  
> Url: https://github.com/boostorg/interprocess/issues/129#issuecomment-770885529  

Closing the bug due to no feedback.
