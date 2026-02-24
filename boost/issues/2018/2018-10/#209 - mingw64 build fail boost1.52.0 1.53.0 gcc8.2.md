# #209 - mingw64 build fail boost1.52.0 1.53.0 gcc8.2 [Closed]

> Username: ZYV037  
> Created at: 2018-10-18 12:57:59 UTC  
> Updated at: 2018-11-27 01:06:07 UTC  
> Closed at: 2018-11-27 01:06:07 UTC  
> Url: https://github.com/boostorg/boost/issues/209  

build command：  
.\b2 -j8 --build-dir=.\tmp  threading=multi link=shared address-model=64 to  
olset=gcc --with-thread --with-chrono --with-date_time  
  
error：  
mp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x216): undefined reference to `InterlockedIncrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x2e9): undefined reference to `InterlockedIncrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x38a): undefined reference to `InterlockedIncrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x397): undefined reference to `InterlockedDecrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x408): undefined reference to `InterlockedIncrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x415): undefined reference to `InterlockedDecrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x479): undefined reference to `InterlockedDecrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x4d9): undefined reference to `InterlockedDecrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x536): undefined reference to `InterlockedIncrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x54b): undefined reference to `InterlockedDecrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x5b9): undefined reference to `InterlockedIncrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x5d6): undefined reference to `InterlockedDecrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x68e): undefined reference to `InterlockedIncrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x69a): undefined reference to `InterlockedDecrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x6f8): undefined reference to `InterlockedIncrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0xed6): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0xef3): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0xf14): undefined reference to `InterlockedExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0xf45): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0xf7d): undefined reference to `InterlockedDecrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x1066): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x108b): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x10d3): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x10f1): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x1112): undefined reference to `InterlockedExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x1151): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x118d): undefined reference to `InterlockedDecrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x129d): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x12f6): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x1317): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x134d): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x13a7): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x13cc): more undefined references to `InterlockedCompareExchange' follow  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x1517): undefined reference to `InterlockedExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x1552): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x159e): undefined reference to `InterlockedDecrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x163e): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x1698): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x16bd): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x26ce): undefined reference to `InterlockedIncrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x2733): undefined reference to `InterlockedDecrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x27c7): undefined reference to `InterlockedIncrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x2820): undefined reference to `InterlockedDecrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x285c): undefined reference to `InterlockedDecrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x360): undefined reference to `InterlockedIncrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text$_ZN5boost6detail21intrusive_ptr_releaseEPNS0_16thread_data_baseE[_ZN5boost6detail21intrusive_ptr_releaseEPNS0_16thread_data_baseE]+0xd): undefined reference to `InterlockedDecrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text$_ZN5boost9call_onceIPFvvEEEvRNS_9once_flagET_[_ZN5boost9call_onceIPFvvEEEvRNS_9once_flagET_]+0x48): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text$_ZN5boost9call_onceIPFvvEEEvRNS_9once_flagET_[_ZN5boost9call_onceIPFvvEEEvRNS_9once_flagET_]+0x63): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text$_ZN5boost9call_onceIPFvvEEEvRNS_9once_flagET_[_ZN5boost9call_onceIPFvvEEEvRNS_9once_flagET_]+0x75): undefined reference to `InterlockedIncrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text$_ZN5boost9call_onceIPFvvEEEvRNS_9once_flagET_[_ZN5boost9call_onceIPFvvEEEvRNS_9once_flagET_]+0x82): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text$_ZN5boost9call_onceIPFvvEEEvRNS_9once_flagET_[_ZN5boost9call_onceIPFvvEEEvRNS_9once_flagET_]+0xd4): undefined reference to `InterlockedIncrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text$_ZN5boost9call_onceIPFvvEEEvRNS_9once_flagET_[_ZN5boost9call_onceIPFvvEEEvRNS_9once_flagET_]+0xe1): undefined reference to `InterlockedExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text$_ZN5boost9call_onceIPFvvEEEvRNS_9once_flagET_[_ZN5boost9call_onceIPFvvEEEvRNS_9once_flagET_]+0x172): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text$_ZN5boost9call_onceIPFvvEEEvRNS_9once_flagET_[_ZN5boost9call_onceIPFvvEEEvRNS_9once_flagET_]+0x1fe): undefined reference to `InterlockedExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text$_ZN5boost6detail17basic_timed_mutex9get_eventEv[_ZN5boost6detail17basic_timed_mutex9get_eventEv]+0x13): undefined reference to `InterlockedCompareExchangePointer'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text$_ZN5boost6detail17basic_timed_mutex9get_eventEv[_ZN5boost6detail17basic_timed_mutex9get_eventEv]+0x52): undefined reference to `InterlockedCompareExchangePointer'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text$_ZN5boost6detail17basic_timed_mutex6unlockEv[_ZN5boost6detail17basic_timed_mutex6unlockEv]+0xf): undefined reference to `InterlockedExchangeAdd'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text$_ZN5boost6detail17basic_timed_mutex6unlockEv[_ZN5boost6detail17basic_timed_mutex6unlockEv]+0x45): undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text$_ZN5boost6detail17basic_timed_mutex6unlockEv[_ZN5boost6detail17basic_timed_mutex6unlockEv]+0x62): undefined reference to `InterlockedCompareExchangePointer'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text$_ZN5boost6detail17basic_timed_mutex6unlockEv[_ZN5boost6detail17basic_timed_mutex6unlockEv]+0xa1): undefined reference to `InterlockedCompareExchangePointer'  
collect2.exe: error: ld returned 1 exit status  
  
    "g++"   "-Wl,--out-implib,tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\libboost_thread-mgw82-mt-1_53.dll.a" -o "tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\libboost_thread-mgw82-mt-1_53.dll"  -shared -Wl,--start-group "tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o" "tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\tss_dll.o" "tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\tss_pe.o" "tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\future.o" "tmp\boost\bin.v2\libs\system\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\libboost_system-mgw82-mt-1_53.dll.a"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -mthreads -m64  
  
...failed gcc.link.dll tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\libboost_thread-mgw82-mt-1_53.dll.a tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\libboost_thread-mgw82-mt-1_53.dll...  
...removing tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\libboost_thread-mgw82-mt-1_53.dll.a  
...skipped <pstage\lib>libboost_thread-mgw82-mt-1_53.dll.a for lack of <ptmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi>libboost_thread-mgw82-mt-1_53.dll.a...  
...skipped <pstage\lib>libboost_thread-mgw82-mt-1_53.dll for lack of <ptmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi>libboost_thread-mgw82-mt-1_53.dll...  
...failed updating 4 targets...  
...skipped 4 targets...  
PS C:\Users\AaronZhang\Desktop\boost_1_53_0> .\b2 -j8 --build-dir=.\tmp  threading=multi link=shared address-model=64 to  
olset=gcc --with-thread --with-chrono --with-date_time

---

## Comment 1

> Username: ZYV037  
> Created at: 2018-10-18 13:55:45 UTC  
> Url: https://github.com/boostorg/boost/issues/209#issuecomment-431018184  

I figure it out by swap boost_1_56_0\boost\detail\interlocked.hpp to boost_1_52_0.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-11-27 00:23:19 UTC  
> Url: https://github.com/boostorg/boost/issues/209#issuecomment-441850862  

@ZYV037 please close the issue if you resolved it.
