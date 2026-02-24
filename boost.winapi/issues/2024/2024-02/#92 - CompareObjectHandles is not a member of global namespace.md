# #92 - CompareObjectHandles is not a member of global namespace [Closed]

> Username: carlocorradini  
> Created at: 2024-02-22 17:24:55 UTC  
> Updated at: 2024-02-27 18:09:56 UTC  
> Closed at: 2024-02-23 12:05:53 UTC  
> Url: https://github.com/boostorg/winapi/issues/92  

I'm using MSVC 14.0 (2015) and building Boost 1.84.0 via [cpm](https://github.com/cpm-cmake/CPM.cmake) (CMake) on Windows 10 Pro:  
```cmake  
CPMAddPackage(  
  NAME boost  
  VERSION 1.84.0  
  URL [omitted]/boost-1.84.0.tar.xz  
  URL_HASH SHA256=2e64e5d79a738d0fa6fb546c6e5c2bd28f88d268a2a080546f74e5ff98f29d0e  
  EXCLUDE_FROM_ALL TRUE  
  SYSTEM TRUE  
  OPTIONS "BOOST_ENABLE_CMAKE TRUE"  
          "BOOST_INCLUDE_LIBRARIES asio\\\;beast\\\;crc\\\;date_time\\\;filesystem\\\;log"  
)  
```  
  
I receive the following error:  
`error C2039: 'CompareObjectHandles': is not a member of 'global namespace''`  
  
`error C2873: 'CompareObjectHandles': symbol cannot be used in a using-declaration`  
  
<img width="863" alt="image" src="https://github.com/boostorg/winapi/assets/21266147/1df16eb7-3738-4800-9938-47987330662d">  
  
What is strange is that the function declaration is inside the same if condition and therefore (depending by the result) should automatically be defined or undefined.  
  
Any idea? Thanks 😅🥳🤯

---

## Comment 1

> Username: Lastique  
> Created at: 2024-02-22 17:40:56 UTC  
> Url: https://github.com/boostorg/winapi/issues/92#issuecomment-1959946026  

The declaration in `boost/winapi/handles.hpp` is only enabled if `BOOST_USE_WINDOWS_H` is not defined. If it is, the declaration is expected to be provided by `windows.h` from Platform SDK.  
  
My guess is that you have `BOOST_USE_WINDOWS_H` defined somewhere, and there is mismatch between target Windows versions requested by `_WIN32_WINNT`, `BOOST_USE_WINAPI_VERSION`, `WINAPI_FAMILY` and `BOOST_USE_WINAPI_FAMILY`. This could make it that `windows.h` doesn't provide `CompareObjectHandles` declaration and Boost.WinAPI tries to use it.  
  
Check your macro definitions. Otherwise, can you provide a small self-contained reproducer?

---

## Comment 2

> Username: carlocorradini  
> Created at: 2024-02-22 22:50:24 UTC  
> Url: https://github.com/boostorg/winapi/issues/92#issuecomment-1960465685  

Could it be that the infamous windows.h is included in my "main" code, followed by the boost/... header?  
  
And something in windows.h enables this strange behavior?  
  
Thanks 🙏

---

## Comment 3

> Username: Lastique  
> Created at: 2024-02-23 01:07:46 UTC  
> Url: https://github.com/boostorg/winapi/issues/92#issuecomment-1960598798  

It is possible that the problem is triggered by the order of includes, yes. But the root cause is not the order of includes, but rather the consistency of defines, as I described earlier. The correct solution is to define version macros like `_WIN32_WINNT` and `WINAPI_FAMILY` in your project or compiler command line, then the order of includes doesn't matter.

---

## Comment 4

> Username: Lastique  
> Created at: 2024-02-23 12:05:53 UTC  
> Url: https://github.com/boostorg/winapi/issues/92#issuecomment-1961210991  

I'm going to close this as I'm not seeing errors in how `CompareObjectHandles` is declared in Boost.WinAPI. The preprocessor conditions match those in MSVC Platform SDK. If you can create a reproducer or have further info, please feel free to comment.

---

## Comment 5

> Username: carlocorradini  
> Created at: 2024-02-26 14:07:55 UTC  
> Url: https://github.com/boostorg/winapi/issues/92#issuecomment-1964230733  

@Lastique  Thank you for your patience  
You can find my extremely minimalistic project at https://github.com/carlocorradini/boost_error  
  
The odd thing is that the compilation problem occurs when building `Boost.log`, even though the main file is mostly empty and has no potential inclusion error order  
  
As you can see from the `#FIXME` comment, I added the `BOOST_USE_WINDOWS_H` definition in various places, however none of them resolved the issue 😢  
  
Any idea? Thanks 🥳

---

## Comment 6

> Username: Lastique  
> Created at: 2024-02-26 14:46:10 UTC  
> Url: https://github.com/boostorg/winapi/issues/92#issuecomment-1964318241  

Doesn't reproduce on my system.  
  
```  
cmake -T v140 -DCMAKE_BUILD_TYPE:STRING=Release -DBUILD_SHARED_LIBS:BOOL=FALSE .. >config.log  
```  
[config.log](https://github.com/boostorg/winapi/files/14406100/config.log)  
  
```  
set VERBOSE=1  
cmake --build . --config Release >build.log  
```  
[build.log](https://github.com/boostorg/winapi/files/14406101/build.log)  
  
Perhaps, you have some outdated Platform SDK installed or running your commands from a different VS command prompt?

---

## Comment 7

> Username: carlocorradini  
> Created at: 2024-02-26 18:07:10 UTC  
> Url: https://github.com/boostorg/winapi/issues/92#issuecomment-1964807788  

**`cmake -S . -B ./build -G "Visual Studio 14 2015" -A x64 -DCMAKE_BUILD_TYPE:STRING=Release`:**  
```sh  
-- The CXX compiler identification is MSVC 19.0.24215.1  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: C:/Program Files (x86)/Microsoft Visual Studio 14.0/VC/bin/x86_amd64/cl.exe - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- CPM: Adding package boost@1.84.0 (1.84.0)  
-- Boost: static libraries, shared runtime, MPI OFF, Python OFF, testing OFF  
-- Boost: libraries included: log  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Failed  
-- Looking for pthread_create in pthreads  
-- Looking for pthread_create in pthreads - not found  
-- Looking for pthread_create in pthread  
-- Looking for pthread_create in pthread - not found  
-- Found Threads: TRUE  
-- Performing Test BOOST_ATOMIC_TARGET_X86  
-- Performing Test BOOST_ATOMIC_TARGET_X86 - Success  
-- Performing Test BOOST_ATOMIC_COMPILER_HAS_SSE2  
-- Performing Test BOOST_ATOMIC_COMPILER_HAS_SSE2 - Success  
-- Performing Test BOOST_ATOMIC_COMPILER_HAS_SSE41  
-- Performing Test BOOST_ATOMIC_COMPILER_HAS_SSE41 - Success  
-- Performing Test BOOST_ATOMIC_HAS_SYNCHRONIZATION_LIB  
-- Performing Test BOOST_ATOMIC_HAS_SYNCHRONIZATION_LIB - Success  
-- The C compiler identification is MSVC 19.0.24215.1  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Check for working C compiler: C:/Program Files (x86)/Microsoft Visual Studio 14.0/VC/bin/x86_amd64/cl.exe - skipped  
-- Detecting C compile features  
-- Detecting C compile features - done  
-- Boost.Context: architecture x86_64, binary format pe, ABI ms, assembler masm, suffix .asm, implementation fcontext  
-- The ASM_MASM compiler identification is MSVC  
-- Found assembler: C:/Program Files (x86)/Microsoft Visual Studio 14.0/VC/bin/x86_amd64/ml64.exe  
-- Performing Test BOOST_FILESYSTEM_HAS_INIT_PRIORITY  
-- Performing Test BOOST_FILESYSTEM_HAS_INIT_PRIORITY - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_CXX20_ATOMIC_REF  
-- Performing Test BOOST_FILESYSTEM_HAS_CXX20_ATOMIC_REF - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BLKSIZE  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BLKSIZE - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIM  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIM - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIMENSEC  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIMENSEC - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIMESPEC  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIMESPEC - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMENSEC  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMENSEC - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMESPEC  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMESPEC - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STATX  
-- Performing Test BOOST_FILESYSTEM_HAS_STATX - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STATX_SYSCALL  
-- Performing Test BOOST_FILESYSTEM_HAS_STATX_SYSCALL - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_FDOPENDIR_NOFOLLOW  
-- Performing Test BOOST_FILESYSTEM_HAS_FDOPENDIR_NOFOLLOW - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_DIRENT_D_TYPE  
-- Performing Test BOOST_FILESYSTEM_HAS_DIRENT_D_TYPE - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_POSIX_AT_APIS  
-- Performing Test BOOST_FILESYSTEM_HAS_POSIX_AT_APIS - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_BCRYPT  
-- Performing Test BOOST_FILESYSTEM_HAS_BCRYPT - Failed  
-- Performing Test BOOST_LOG_TARGET_X86  
-- Performing Test BOOST_LOG_TARGET_X86 - Success  
-- Performing Test BOOST_LOG_HAS_LOCK_FREE_ATOMIC_INT32  
-- Performing Test BOOST_LOG_HAS_LOCK_FREE_ATOMIC_INT32 - Success  
-- Performing Test BOOST_LOG_COMPILER_HAS_SSSE3  
-- Performing Test BOOST_LOG_COMPILER_HAS_SSSE3 - Success  
-- Performing Test BOOST_LOG_COMPILER_HAS_AVX2  
-- Performing Test BOOST_LOG_COMPILER_HAS_AVX2 - Success  
-- Performing Test BOOST_LOG_HAS_NATIVE_SYSLOG  
-- Performing Test BOOST_LOG_HAS_NATIVE_SYSLOG - Failed  
-- Boost.Log: Message compiler mc.exe not found, event log support will be disabled.  
-- Boost.Thread: threading API is win32  
-- Configuring done (121.4s)  
-- Generating done (1.4s)  
-- Build files have been written to: C:/boost_error/build  
```  
  
**`cmake --build ./build --config Release`:**  
```sh  
Microsoft (R) Build Engine version 14.0.25420.1  
Copyright (C) Microsoft Corporation. All rights reserved.  
  
  Checking Build System  
  Building Custom Rule C:/boost_error/build/_deps/boost-src/libs/assert/CMakeLists.txt  
  Building Custom Rule C:/boost_error/build/_deps/boost-src/libs/core/CMakeLists.txt  
  Building Custom Rule C:/boost_error/build/_deps/boost-src/libs/system/CMakeLists.txt  
  Building Custom Rule C:/boost_error/build/_deps/boost-src/libs/variant2/CMakeLists.txt  
  Building Custom Rule C:/boost_error/build/_deps/boost-src/libs/atomic/CMakeLists.txt  
  lock_pool.cpp  
  find_address_sse2.cpp  
  find_address_sse41.cpp  
  wait_on_address.cpp  
  Generating Code...  
  boost_atomic.vcxproj -> C:\boost_error\build\_deps\boost-build\libs\atomic\Release\libboost_atomic-vc140-mt-x64-1_84.lib  
  Building Custom Rule C:/boost_error/build/_deps/boost-src/libs/chrono/CMakeLists.txt  
  chrono.cpp  
  process_cpu_clocks.cpp  
  thread_clock.cpp  
  Generating Code...  
  boost_chrono.vcxproj -> C:\boost_error\build\_deps\boost-build\libs\chrono\Release\libboost_chrono-vc140-mt-x64-1_84.lib  
  Building Custom Rule C:/boost_error/build/_deps/boost-src/libs/container/CMakeLists.txt  
  alloc_lib.c  
  dlmalloc.cpp  
  global_resource.cpp  
  monotonic_buffer_resource.cpp  
  pool_resource.cpp  
  synchronized_pool_resource.cpp  
  unsynchronized_pool_resource.cpp  
  Generating Code...  
  boost_container.vcxproj -> C:\boost_error\build\_deps\boost-build\libs\container\Release\libboost_container-vc140-mt-x64-1_84.lib  
  Building Custom Rule C:/boost_error/build/_deps/boost-src/libs/context/CMakeLists.txt  
  Assembling C:\boost_error\build\_deps\boost-src\libs\context\src\asm\make_x86_64_ms_pe_masm.asm...  
  Assembling C:\boost_error\build\_deps\boost-src\libs\context\src\asm\jump_x86_64_ms_pe_masm.asm...  
  Assembling C:\boost_error\build\_deps\boost-src\libs\context\src\asm\ontop_x86_64_ms_pe_masm.asm...  
  stack_traits.cpp  
  boost_context.vcxproj -> C:\boost_error\build\_deps\boost-build\libs\context\Release\libboost_context-vc140-mt-x64-1_84.lib  
  Building Custom Rule C:/boost_error/build/_deps/boost-src/libs/exception/CMakeLists.txt  
  clone_current_exception_non_intrusive.cpp  
  boost_exception.vcxproj -> C:\boost_error\build\_deps\boost-build\libs\exception\Release\libboost_exception-vc140-mt-x64-1_84.lib  
  Building Custom Rule C:/boost_error/build/_deps/boost-src/libs/coroutine/CMakeLists.txt  
  coroutine_context.cpp  
  exceptions.cpp  
  stack_traits.cpp  
  Generating Code...  
  boost_coroutine.vcxproj -> C:\boost_error\build\_deps\boost-build\libs\coroutine\Release\libboost_coroutine-vc140-mt-x64-1_84.lib  
  Building Custom Rule C:/boost_error/build/_deps/boost-src/libs/date_time/CMakeLists.txt  
  greg_month.cpp  
  boost_date_time.vcxproj -> C:\boost_error\build\_deps\boost-build\libs\date_time\Release\libboost_date_time-vc140-mt-x64-1_84.lib  
  Building Custom Rule C:/boost_error/build/_deps/boost-src/libs/filesystem/CMakeLists.txt  
  codecvt_error_category.cpp  
  exception.cpp  
  operations.cpp  
  directory.cpp  
  path.cpp  
  path_traits.cpp  
  portability.cpp  
  unique_path.cpp  
  utf8_codecvt_facet.cpp  
  windows_file_codecvt.cpp  
  Generating Code...  
  boost_filesystem.vcxproj -> C:\boost_error\build\_deps\boost-build\libs\filesystem\Release\libboost_filesystem-vc140-mt-x64-1_84.lib  
  Building Custom Rule C:/boost_error/build/_deps/boost-src/libs/random/CMakeLists.txt  
  random_device.cpp  
  boost_random.vcxproj -> C:\boost_error\build\_deps\boost-build\libs\random\Release\libboost_random-vc140-mt-x64-1_84.lib  
  Building Custom Rule C:/boost_error/build/_deps/boost-src/libs/thread/CMakeLists.txt  
  thread.cpp  
C:\boost_error\build\_deps\boost-src\libs\winapi\include\boost/winapi/handles.hpp(50): error C2039: 'CompareObjectHandles': is not a member of '`global namespace'' [C:\boost_error\build\_deps\boost-build\libs\thread\boost_thread.vcxproj]  
C:\boost_error\build\_deps\boost-src\libs\winapi\include\boost/winapi/handles.hpp(50): error C2873: 'CompareObjectHandles': symbol cannot be used in a using-declaration [C:\boost_error\build\_deps\boost-build\libs\thread\boost_thread.vcxproj]  
  tss_dll.cpp  
  tss_pe.cpp  
  thread_primitives.cpp  
C:\boost_error\build\_deps\boost-src\libs\winapi\include\boost/winapi/handles.hpp(50): error C2039: 'CompareObjectHandles': is not a member of '`global namespace'' [C:\boost_error\build\_deps\boost-build\libs\thread\boost_thread.vcxproj]  
C:\boost_error\build\_deps\boost-src\libs\winapi\include\boost/winapi/handles.hpp(50): error C2873: 'CompareObjectHandles': symbol cannot be used in a using-declaration [C:\boost_error\build\_deps\boost-build\libs\thread\boost_thread.vcxproj]  
  future.cpp  
  Generating Code...  
```  
  
@Lastique Any idea? I really don't know what is the cause 😢

---

## Comment 8

> Username: Lastique  
> Created at: 2024-02-26 18:52:22 UTC  
> Url: https://github.com/boostorg/winapi/issues/92#issuecomment-1964921651  

My only idea is that there's something wrong with your Platform SDK. I can't be any more specific, sorry.  
  
What Platform SDK version are you using? It is not mentioned in your configure output.

---

## Comment 9

> Username: carlocorradini  
> Created at: 2024-02-27 18:09:55 UTC  
> Url: https://github.com/boostorg/winapi/issues/92#issuecomment-1967322965  

@Lastique Thanks for all the support!  
  
I had a conflict due to Windows Kits 8.1 set as default SDK in MSVC 14.0 2015  
  
See https://stackoverflow.com/questions/34631913/how-to-set-the-default-windows-kit-sdk-version for more information
