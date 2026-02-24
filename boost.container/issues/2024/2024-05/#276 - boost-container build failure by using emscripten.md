# #276 - boost-container build failure by using emscripten [Closed]

> Username: rezzeted  
> Created at: 2024-05-16 22:28:16 UTC  
> Updated at: 2025-09-04 08:57:28 UTC  
> Closed at: 2024-05-23 21:47:14 UTC  
> Url: https://github.com/boostorg/container/issues/276  

Package: boost-container:wasm32-emscripten@1.85.0  
  
**Host Environment**  
  
- Host: x64-windows  
- Compiler: Clang 19.0.0  
-    vcpkg-tool version: 2024-04-23-d6945642ee5c3076addd1a42c331bbf4cfc97457  
    vcpkg-scripts version: unknown  
  
**To Reproduce**  
  
`vcpkg install `  
  
**Failure logs**  
  
```  
-- Using cached boostorg-container-boost-1.85.0.tar.gz.  
-- Cleaning sources at D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean. Use --editable to skip cleaning for the packages you specify.  
-- Extracting source D:/sandbox/sandbox/toolchain/vcpkg/downloads/boostorg-container-boost-1.85.0.tar.gz  
-- Using source at D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean  
-- Configuring wasm32-emscripten  
-- Building wasm32-emscripten-dbg  
CMake Error at scripts/cmake/vcpkg_execute_build_process.cmake:134 (message):  
    Command failed: D:/sandbox/sandbox/toolchain/vcpkg/downloads/tools/cmake-3.29.2-windows/cmake-3.29.2-windows-i386/bin/cmake.exe --build . --config Debug --target install -- -v -j33  
    Working Directory: D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/wasm32-emscripten-dbg  
    See logs for more information:  
      D:\sandbox\sandbox\toolchain\vcpkg\buildtrees\boost-container\install-wasm32-emscripten-dbg-out.log  
  
Call Stack (most recent call first):  
  D:/sandbox/sandbox/_obj-lib-etc-em/vcpkg_installed/x64-windows/share/vcpkg-cmake/vcpkg_cmake_build.cmake:74 (vcpkg_execute_build_process)  
  D:/sandbox/sandbox/_obj-lib-etc-em/vcpkg_installed/x64-windows/share/vcpkg-cmake/vcpkg_cmake_install.cmake:16 (vcpkg_cmake_build)  
  D:/sandbox/sandbox/_obj-lib-etc-em/vcpkg_installed/x64-windows/share/vcpkg-boost/boost-install.cmake:65 (vcpkg_cmake_install)  
  ports/boost-container/portfile.cmake:12 (boost_configure_and_install)  
  scripts/ports.cmake:175 (include)  
  
  
  
```  
  
<details><summary>D:\sandbox\sandbox\toolchain\vcpkg\buildtrees\boost-container\install-wasm32-emscripten-dbg-out.log</summary>  
  
```  
Change Dir: 'D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/wasm32-emscripten-dbg'  
  
Run Build Command(s): D:/sandbox/sandbox/toolchain/vcpkg/downloads/tools/ninja/1.10.2-windows/ninja.exe -v -v -j33 install  
[1/9] D:\_compilers\emsdk\upstream\emscripten\em++.bat -DBOOST_CONTAINER_NO_LIB -DBOOST_CONTAINER_STATIC_LINK -ID:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/include -isystem D:/sandbox/sandbox/_obj-lib-etc-em/vcpkg_installed/wasm32-emscripten/include -g -fvisibility=hidden -fvisibility-inlines-hidden -MD -MT libs/container/CMakeFiles/boost_container.dir/src/dlmalloc.cpp.o -MF libs\container\CMakeFiles\boost_container.dir\src\dlmalloc.cpp.o.d -o libs/container/CMakeFiles/boost_container.dir/src/dlmalloc.cpp.o -c D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/src/dlmalloc.cpp  
[2/9] D:\_compilers\emsdk\upstream\emscripten\em++.bat -DBOOST_CONTAINER_NO_LIB -DBOOST_CONTAINER_STATIC_LINK -ID:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/include -isystem D:/sandbox/sandbox/_obj-lib-etc-em/vcpkg_installed/wasm32-emscripten/include -g -fvisibility=hidden -fvisibility-inlines-hidden -MD -MT libs/container/CMakeFiles/boost_container.dir/src/synchronized_pool_resource.cpp.o -MF libs\container\CMakeFiles\boost_container.dir\src\synchronized_pool_resource.cpp.o.d -o libs/container/CMakeFiles/boost_container.dir/src/synchronized_pool_resource.cpp.o -c D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/src/synchronized_pool_resource.cpp  
FAILED: libs/container/CMakeFiles/boost_container.dir/src/synchronized_pool_resource.cpp.o   
D:\_compilers\emsdk\upstream\emscripten\em++.bat -DBOOST_CONTAINER_NO_LIB -DBOOST_CONTAINER_STATIC_LINK -ID:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/include -isystem D:/sandbox/sandbox/_obj-lib-etc-em/vcpkg_installed/wasm32-emscripten/include -g -fvisibility=hidden -fvisibility-inlines-hidden -MD -MT libs/container/CMakeFiles/boost_container.dir/src/synchronized_pool_resource.cpp.o -MF libs\container\CMakeFiles\boost_container.dir\src\synchronized_pool_resource.cpp.o.d -o libs/container/CMakeFiles/boost_container.dir/src/synchronized_pool_resource.cpp.o -c D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/src/synchronized_pool_resource.cpp  
In file included from D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/src/synchronized_pool_resource.cpp:14:  
D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/include\boost/container/detail/thread_mutex.hpp:109:12: error: '__declspec' attributes are not enabled; use '-fdeclspec' or '-fms-extensions' to enable support for __declspec attributes  
  109 | extern "C" __declspec(dllimport) void __stdcall InitializeCriticalSection(::_RTL_CRITICAL_SECTION *);  
      |            ^  
D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/include\boost/container/detail/thread_mutex.hpp:109:39: warning: '__stdcall' calling convention is not supported for this target [-Wignored-attributes]  
  109 | extern "C" __declspec(dllimport) void __stdcall InitializeCriticalSection(::_RTL_CRITICAL_SECTION *);  
      |                                       ^  
D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/include\boost/container/detail/thread_mutex.hpp:111:12: error: '__declspec' attributes are not enabled; use '-fdeclspec' or '-fms-extensions' to enable support for __declspec attributes  
  111 | extern "C" __declspec(dllimport) void __stdcall EnterCriticalSection(::_RTL_CRITICAL_SECTION *);  
      |            ^  
D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/include\boost/container/detail/thread_mutex.hpp:111:39: warning: '__stdcall' calling convention is not supported for this target [-Wignored-attributes]  
  111 | extern "C" __declspec(dllimport) void __stdcall EnterCriticalSection(::_RTL_CRITICAL_SECTION *);  
      |                                       ^  
D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/include\boost/container/detail/thread_mutex.hpp:112:12: error: '__declspec' attributes are not enabled; use '-fdeclspec' or '-fms-extensions' to enable support for __declspec attributes  
  112 | extern "C" __declspec(dllimport) void __stdcall LeaveCriticalSection(::_RTL_CRITICAL_SECTION *);  
      |            ^  
D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/include\boost/container/detail/thread_mutex.hpp:112:39: warning: '__stdcall' calling convention is not supported for this target [-Wignored-attributes]  
  112 | extern "C" __declspec(dllimport) void __stdcall LeaveCriticalSection(::_RTL_CRITICAL_SECTION *);  
      |                                       ^  
D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/include\boost/container/detail/thread_mutex.hpp:113:12: error: '__declspec' attributes are not enabled; use '-fdeclspec' or '-fms-extensions' to enable support for __declspec attributes  
  113 | extern "C" __declspec(dllimport) void __stdcall DeleteCriticalSection(::_RTL_CRITICAL_SECTION *);  
      |            ^  
D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/include\boost/container/detail/thread_mutex.hpp:113:39: warning: '__stdcall' calling convention is not supported for this target [-Wignored-attributes]  
  113 | extern "C" __declspec(dllimport) void __stdcall DeleteCriticalSection(::_RTL_CRITICAL_SECTION *);  
      |                                       ^  
4 warnings and 4 errors generated.  
[3/9] D:\_compilers\emsdk\upstream\emscripten\em++.bat -DBOOST_CONTAINER_NO_LIB -DBOOST_CONTAINER_STATIC_LINK -ID:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/include -isystem D:/sandbox/sandbox/_obj-lib-etc-em/vcpkg_installed/wasm32-emscripten/include -g -fvisibility=hidden -fvisibility-inlines-hidden -MD -MT libs/container/CMakeFiles/boost_container.dir/src/unsynchronized_pool_resource.cpp.o -MF libs\container\CMakeFiles\boost_container.dir\src\unsynchronized_pool_resource.cpp.o.d -o libs/container/CMakeFiles/boost_container.dir/src/unsynchronized_pool_resource.cpp.o -c D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/src/unsynchronized_pool_resource.cpp  
[4/9] D:\_compilers\emsdk\upstream\emscripten\em++.bat -DBOOST_CONTAINER_NO_LIB -DBOOST_CONTAINER_STATIC_LINK -ID:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/include -isystem D:/sandbox/sandbox/_obj-lib-etc-em/vcpkg_installed/wasm32-emscripten/include -g -fvisibility=hidden -fvisibility-inlines-hidden -MD -MT libs/container/CMakeFiles/boost_container.dir/src/monotonic_buffer_resource.cpp.o -MF libs\container\CMakeFiles\boost_container.dir\src\monotonic_buffer_resource.cpp.o.d -o libs/container/CMakeFiles/boost_container.dir/src/monotonic_buffer_resource.cpp.o -c D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/src/monotonic_buffer_resource.cpp  
[5/9] D:\_compilers\emsdk\upstream\emscripten\em++.bat -DBOOST_CONTAINER_NO_LIB -DBOOST_CONTAINER_STATIC_LINK -ID:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/include -isystem D:/sandbox/sandbox/_obj-lib-etc-em/vcpkg_installed/wasm32-emscripten/include -g -fvisibility=hidden -fvisibility-inlines-hidden -MD -MT libs/container/CMakeFiles/boost_container.dir/src/pool_resource.cpp.o -MF libs\container\CMakeFiles\boost_container.dir\src\pool_resource.cpp.o.d -o libs/container/CMakeFiles/boost_container.dir/src/pool_resource.cpp.o -c D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/src/pool_resource.cpp  
[6/9] D:\_compilers\emsdk\upstream\emscripten\em++.bat -DBOOST_CONTAINER_NO_LIB -DBOOST_CONTAINER_STATIC_LINK -ID:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/include -isystem D:/sandbox/sandbox/_obj-lib-etc-em/vcpkg_installed/wasm32-emscripten/include -g -fvisibility=hidden -fvisibility-inlines-hidden -MD -MT libs/container/CMakeFiles/boost_container.dir/src/global_resource.cpp.o -MF libs\container\CMakeFiles\boost_container.dir\src\global_resource.cpp.o.d -o libs/container/CMakeFiles/boost_container.dir/src/global_resource.cpp.o -c D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/src/global_resource.cpp  
[7/9] D:\_compilers\emsdk\upstream\emscripten\emcc.bat -DBOOST_CONTAINER_NO_LIB -DBOOST_CONTAINER_STATIC_LINK -ID:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/include -isystem D:/sandbox/sandbox/_obj-lib-etc-em/vcpkg_installed/wasm32-emscripten/include -g -fvisibility=hidden -MD -MT libs/container/CMakeFiles/boost_container.dir/src/alloc_lib.c.o -MF libs\container\CMakeFiles\boost_container.dir\src\alloc_lib.c.o.d -o libs/container/CMakeFiles/boost_container.dir/src/alloc_lib.c.o -c D:/sandbox/sandbox/toolchain/vcpkg/buildtrees/boost-container/src/ost-1.85.0-b589bd4c0b.clean/libs/container/src/alloc_lib.c  
ninja: build stopped: subcommand failed.  
```  
</details>  
  
**Additional context**  
  
<details><summary>vcpkg.json</summary>

---

## Comment 1

> Username: rezzeted  
> Created at: 2024-05-16 22:31:16 UTC  
> Url: https://github.com/boostorg/container/issues/276#issuecomment-2116309779  

[Error on vcpkg](https://github.com/microsoft/vcpkg/issues/38775)

---

## Comment 2

> Username: dg0yt  
> Created at: 2024-05-18 06:23:29 UTC  
> Url: https://github.com/boostorg/container/issues/276#issuecomment-2118661206  

Basically it uses windows code because it doesn't see pthreads.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2024-05-23 21:47:14 UTC  
> Url: https://github.com/boostorg/container/issues/276#issuecomment-2128071943  

Sorry but the library does not support emscriptem and it's not tested in that platform.  
  
It seems that Emscriptem wronly defines "_POSIX_THREADS " as -1, since POSIX defines that meaning quite well:  
  
_If a symbolic constant is defined with the value -1, the option is not supported. Headers, data types, and function interfaces required only for the option need not be supplied. An application that attempts to use anything associated only with the option is considered to be requiring an extension._

---

## Comment 4

> Username: dg0yt  
> Created at: 2024-05-24 04:28:23 UTC  
> Url: https://github.com/boostorg/container/issues/276#issuecomment-2128499230  

The `_POSIX_THREADS` value on emscripten changes with `-pthread` which is the official way to enable it.  
https://emscripten.org/docs/porting/pthreads.html#compiling-with-pthreads-enabled  
However, even cmake's  
~~~  
  set(THREADS_PREFER_PTHREAD_FLAG 1)  
  find_package(Threads REQUIRED)  
~~~  
doesn't add the flag, so it has to be done explicitly.  
  
https://github.com/microsoft/vcpkg/pull/38806/files#diff-8728338e87238c609e4a490888aebbc1d428166bdc0a0b6b1303281b27732a0e  
  
The other point is that the (CMake) build behaviour is misleading: If it doesn't find POSIX threads, it immediately uses Windows code, even on non-Windows platforms. This leads to unexpected error messages which do not point to the lack of POSIX threads.

---

## Comment 5

> Username: DoDoENT  
> Created at: 2025-09-04 08:57:27 UTC  
> Url: https://github.com/boostorg/container/issues/276#issuecomment-3252588516  

I stumbled upon this same issue also when building with Bazel ([boost.container BCR module](https://registry.bazel.build/modules/boost.container)). It builds correctly when using emscripten with threads (i.e. with `-pthread` flag), however, using `-pthread` generates WASM code that is not compatible with all browsers - some browsers don't support threading, or their support is broken (Safari, I'm looking at you). Therefore, we also build our code without thread support.  
  
Emscripten does not define `_POSIX_THREADS` for non-threaded builds, although the stub functions for posix threads are available. My current workaround is to simply add `-DBOOST_HAS_PTHREADS` for all emscripten builds globally, but it is a dirty hack.  
  
I'm not sure where the bug actually is:  
  
- emscripten with disabled thread support is correct to not define `_POSIX_THREADS`, in my opinion (which may be wrong)  
- `boost.config` should keep using the `_POSIX_THREAD` to detect whether threads are available  
- `boost.container` should not assume that if `BOOST_HAS_PTHREADS` is not defined, that it's being compiled on Windows.   
    - If it's a single-threaded build (emscripten without threads or even embedded platforms or bare-metal targets that don't support threads), it should have an empty implementation of `boost::container::dtl::thread_mutex` which simply doesn't do anything.
