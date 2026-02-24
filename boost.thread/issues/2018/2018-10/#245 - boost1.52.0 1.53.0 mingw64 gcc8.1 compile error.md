# #245 - boost1.52.0 1.53.0 mingw64 gcc8.1 compile error [Closed]

> Username: ZYV037  
> Created at: 2018-10-18 13:03:55 UTC  
> Updated at: 2018-11-12 18:25:13 UTC  
> Closed at: 2018-11-12 18:25:13 UTC  
> Url: https://github.com/boostorg/thread/issues/245  

.\b2 -j8 --build-dir=.\tmp  threading=multi link=shared address-model=64 to  
olset=gcc --with-thread --with-chrono --with-date_time  
  
...patience...  
...found 1240 targets...  
...updating 8 targets...  
gcc.link.dll tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\libboost_thread-mgw82-mt-d-1_53.dll.a  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\win32\thread.o: In function `boost::detail::win32::interlocked_bit_test_and_set(long*, long)':  
C:\Users\AaronZhang\Desktop\boost_1_53_0/./boost/thread/win32/thread_primitives.hpp:408: undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\win32\thread.o: In function `boost::detail::intrusive_ptr_add_ref(boost::detail::thread_data_base*)':  
C:\Users\AaronZhang\Desktop\boost_1_53_0/./boost/thread/win32/thread_data.hpp:130: undefined reference to `InterlockedIncrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\win32\thread.o: In function `boost::detail::intrusive_ptr_release(boost::detail::thread_data_base*)':  
C:\Users\AaronZhang\Desktop\boost_1_53_0/./boost/thread/win32/thread_data.hpp:135: undefined reference to `InterlockedDecrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\win32\thread.o: In function `boost::detail::interlocked_read_acquire(long volatile*)':  
C:\Users\AaronZhang\Desktop\boost_1_53_0/./boost/thread/win32/interlocked_read.hpp:61: undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\win32\thread.o: In function `boost::detail::interlocked_read_acquire(void* volatile*)':  
C:\Users\AaronZhang\Desktop\boost_1_53_0/./boost/thread/win32/interlocked_read.hpp:65: undefined reference to `InterlockedCompareExchangePointer'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\win32\thread.o: In function `boost::detail::interlocked_write_release(long volatile*, long)':  
C:\Users\AaronZhang\Desktop\boost_1_53_0/./boost/thread/win32/interlocked_read.hpp:69: undefined reference to `InterlockedExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\win32\thread.o: In function `boost::detail::basic_timed_mutex::mark_waiting_and_try_lock(long&)':  
C:\Users\AaronZhang\Desktop\boost_1_53_0/./boost/thread/win32/basic_timed_mutex.hpp:97: undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\win32\thread.o: In function `boost::detail::basic_timed_mutex::clear_waiting_and_try_lock(long&)':  
C:\Users\AaronZhang\Desktop\boost_1_53_0/./boost/thread/win32/basic_timed_mutex.hpp:113: undefined reference to `InterlockedCompareExchange'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\win32\thread.o: In function `boost::detail::basic_timed_mutex::unlock()':  
C:\Users\AaronZhang\Desktop\boost_1_53_0/./boost/thread/win32/basic_timed_mutex.hpp:220: undefined reference to `InterlockedExchangeAdd'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\win32\thread.o: In function `boost::detail::basic_timed_mutex::get_event()':  
C:\Users\AaronZhang\Desktop\boost_1_53_0/./boost/thread/win32/basic_timed_mutex.hpp:243: undefined reference to `InterlockedCompareExchangePointer'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\win32\thread.o: In function `boost::detail::intrusive_ptr_release(boost::detail::basic_cv_list_entry*)':  
C:\Users\AaronZhang\Desktop\boost_1_53_0/./boost/thread/win32/condition_variable.hpp:115: undefined reference to `InterlockedDecrement'  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\win32\thread.o: In function `void boost::call_once<void (*)()>(boost::once_flag&, void (*)())':  
C:\Users\AaronZhang\Desktop\boost_1_53_0/./boost/thread/win32/once.hpp:158: undefined reference to `InterlockedCompareExchange'  
C:\Users\AaronZhang\Desktop\boost_1_53_0/./boost/thread/win32/once.hpp:174: undefined reference to `InterlockedIncrement'  
C:\Users\AaronZhang\Desktop\boost_1_53_0/./boost/thread/win32/once.hpp:177: undefined reference to `InterlockedExchange'  
C:\Users\AaronZhang\Desktop\boost_1_53_0/./boost/thread/win32/once.hpp:207: undefined reference to `InterlockedIncrement'  
C:\Users\AaronZhang\Desktop\boost_1_53_0/./boost/thread/win32/once.hpp:191: undefined reference to `InterlockedExchange'  
collect2.exe: error: ld returned 1 exit status  
  
    "g++"   "-Wl,--out-implib,tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\libboost_thread-mgw82-mt-d-1_53.dll.a" -o "tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\libboost_thread-mgw82-mt-d-1_53.dll"  -shared -Wl,--start-group "tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\win32\thread.o" "tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\win32\tss_dll.o" "tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\win32\tss_pe.o" "tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\future.o" "tmp\boost\bin.v2\libs\system\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\libboost_system-mgw82-mt-d-1_53.dll.a"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -g -mthreads -m64  
  
...failed gcc.link.dll tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\libboost_thread-mgw82-mt-d-1_53.dll.a tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\libboost_thread-mgw82-mt-d-1_53.dll...  
...removing tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi\libboost_thread-mgw82-mt-d-1_53.dll.a  
...skipped <pstage\lib>libboost_thread-mgw82-mt-d-1_53.dll.a for lack of <ptmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi>libboost_thread-mgw82-mt-d-1_53.dll.a...  
...skipped <pstage\lib>libboost_thread-mgw82-mt-d-1_53.dll for lack of <ptmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\debug\address-model-64\threading-multi>libboost_thread-mgw82-mt-d-1_53.dll...  
gcc.link.dll tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\libboost_thread-mgw82-mt-1_53.dll.a  
tmp\boost\bin.v2\libs\thread\build\gcc-mingw-8.2.0\release\address-model-64\threading-multi\win32\thread.o:thread.cpp:(.text+0x216): undefined reference to `InterlockedIncrement'  
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
> Created at: 2018-10-18 14:00:32 UTC  
> Url: https://github.com/boostorg/thread/issues/245#issuecomment-431020008  

I have figure it out by swap boost_1_56_0\boost\detail\interlocked.hpp to boost_1_52_0.the boost_1_53_0  
can solve by this either.

---

## Comment 2

> Username: viboes  
> Created at: 2018-10-18 16:54:40 UTC  
> Url: https://github.com/boostorg/thread/issues/245#issuecomment-431083980  

Hi,   
  
where InterlockedExchange shoud be defined?   
Have you checked it?  
This should surely help.

---

## Comment 3

> Username: ZYV037  
> Created at: 2018-10-18 23:37:09 UTC  
> Url: https://github.com/boostorg/thread/issues/245#issuecomment-431198554  

Replace 1.52 153 boost/detail/interlocked.hpp by 1.56’s.checked.

---

## Comment 4

> Username: ZYV037  
> Created at: 2018-10-19 01:13:04 UTC  
> Url: https://github.com/boostorg/thread/issues/245#issuecomment-431213146  

after replaced 1.52 153 boost/detail/interlocked.hpp by 1.56’s, you have to clean the build path before.

---

## Comment 5

> Username: viboes  
> Created at: 2018-10-25 01:47:25 UTC  
> Url: https://github.com/boostorg/thread/issues/245#issuecomment-432884976  

Sorry, do you mean that this issue is relative to 1.52/1.53 and that it doesn't appears now as for 1.68?

---

## Comment 6

> Username: jeking3  
> Created at: 2018-11-11 22:34:02 UTC  
> Url: https://github.com/boostorg/thread/issues/245#issuecomment-437711582  

If it still happens in 1.68.0 let us know.  There won't be any fixes for older releases.

---

## Comment 7

> Username: ZYV037  
> Created at: 2018-11-12 11:18:19 UTC  
> Url: https://github.com/boostorg/thread/issues/245#issuecomment-437844101  

as I checked, 1.68 detail\interlocked.hpp is fine. 1.52/1.53 can be fixed by using 1.68's interlocked.hpp for sure.

---

## Comment 8

> Username: ZYV037  
> Created at: 2018-11-12 11:20:47 UTC  
> Url: https://github.com/boostorg/thread/issues/245#issuecomment-437844727  

>           Sorry, do you mean that this issue is relative to 1.52/1.53 and that it doesn't appears now as for 1.68?  
  
yep.

---

## Comment 9

> Username: jeking3  
> Created at: 2018-11-12 11:35:07 UTC  
> Url: https://github.com/boostorg/thread/issues/245#issuecomment-437848272  

Then I would recommend closing this issue.  It has already been fixed. 1.5x  
is not going to be changed.  
  
On Mon, Nov 12, 2018, 6:20 AM AaronZhang <notifications@github.com wrote:  
  
>       Sorry, do you mean that this issue is relative to 1.52/1.53 and that it doesn't appears now as for 1.68?  
>  
> yep.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/thread/issues/245#issuecomment-437844727>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ALOdbadifNcwy3MtT1qdxAWybhWzZxskks5uuVmQgaJpZM4XtZDH>  
> .  
>

---

## Comment 10

> Username: viboes  
> Created at: 2018-11-12 18:25:13 UTC  
> Url: https://github.com/boostorg/thread/issues/245#issuecomment-437981890  

Closed as already fixed
