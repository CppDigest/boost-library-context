# #76 - MinGW compile errors [Closed]

> Username: pdimov  
> Created at: 2025-08-14 21:51:06 UTC  
> Updated at: 2025-08-15 08:21:39 UTC  
> Closed at: 2025-08-15 00:36:06 UTC  
> Url: https://github.com/boostorg/atomic/issues/76  

I'm getting this when building Boost.Atomic under MinGW (from the smart_ptr CI):  
```  
gcc.compile.c++ bin.v2\libs\atomic\build\gcc-5\release\x86_32\cxxstd-11-iso\threadapi-win32\threading-multi\visibility-hidden\lock_pool.o  
In file included from C:/projects/boost-root/libs/atomic/src/lock_pool.cpp:56:0:  
./boost/winapi/srw_lock.hpp: In function 'boost::winapi::VOID_ boost::winapi::InitializeSRWLock(boost::winapi::PSRWLOCK_)':  
./boost/winapi/srw_lock.hpp:74:5: error: '::InitializeSRWLock' has not been declared  
     ::InitializeSRWLock(reinterpret_cast< ::_RTL_SRWLOCK* >(SRWLock));  
     ^  
./boost/winapi/srw_lock.hpp:74:5: note: suggested alternative:  
./boost/winapi/srw_lock.hpp:72:25: note:   'boost::winapi::InitializeSRWLock'  
 BOOST_FORCEINLINE VOID_ InitializeSRWLock(PSRWLOCK_ SRWLock)  
                         ^  
./boost/winapi/srw_lock.hpp:74:45: error: '_RTL_SRWLOCK' in namespace '::' does not name a type  
     ::InitializeSRWLock(reinterpret_cast< ::_RTL_SRWLOCK* >(SRWLock));  
                                             ^  
./boost/winapi/srw_lock.hpp:74:57: error: expected '>' before '*' token  
     ::InitializeSRWLock(reinterpret_cast< ::_RTL_SRWLOCK* >(SRWLock));  
                                                         ^  
```  
https://ci.appveyor.com/project/cppalliance/smart-ptr/builds/52566676

---

## Comment 1

> Username: Lastique  
> Created at: 2025-08-15 00:36:06 UTC  
> Url: https://github.com/boostorg/atomic/issues/76#issuecomment-3190287849  

For the MinGW-w64 build:  
  
```  
C:/mingw-w64/x86_64-7.2.0-posix-seh-rt_v5-rev1/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/7.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: cannot find -lsynchronization  
```  
  
https://ci.appveyor.com/project/cppalliance/smart-ptr/builds/52566676/job/im8x35o8r7955n7v?fullLog=true#L505  
  
This compiler does not provide the `synchronization.lib`, which is now a requirement. You can switch to gcc 7.3.0 or 8.1.0, see [here](https://github.com/boostorg/atomic/blob/8a6de6d93dbe123134deff264851a18d96063764/appveyor.yml#L50-L61) for example.  
  
Legacy MinGW doesn't support `WaitOnAddress` API that is now required and therefore is not supported. I'm not sure why it fails to find the `SRWLOCK` and `CONDITION_VARIABLE` stuff, though; those APIs are present in the MinGW headers I have. Perhaps, AppVeyor has outdated MinGW headers.

---

## Comment 2

> Username: Lastique  
> Created at: 2025-08-15 08:21:39 UTC  
> Url: https://github.com/boostorg/atomic/issues/76#issuecomment-3190927209  

> I'm not sure why it fails to find the `SRWLOCK` and `CONDITION_VARIABLE` stuff, though; those APIs are present in the MinGW headers I have.  
  
These errors should be resolved after https://github.com/boostorg/winapi/commit/39abf0c64c6bfe2c1753f83ca8b56874d0fe3b20. The compilation will still fail due to missing `WaitOnAddress` and `synchronization.lib`.
