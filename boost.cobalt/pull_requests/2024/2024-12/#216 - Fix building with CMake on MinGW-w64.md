# #216 Fix building with CMake on MinGW-w64. [Closed]

> Username: MehdiChinoune  
> Created at: 2024-12-15 05:18:15 UTC  
> Updated at: 2025-06-24 16:33:35 UTC  
> Closed at: 2025-06-24 16:00:09 UTC  
> Url: https://github.com/boostorg/cobalt/pull/216  



---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2024-12-18 08:51:44 UTC  
> Url: https://github.com/boostorg/cobalt/pull/216#issuecomment-2550722186  

Which symbol is missing?

---

## Comment 2

> Username: MehdiChinoune  
> Created_at: 2024-12-18 09:37:47 UTC  
> Url: https://github.com/boostorg/cobalt/pull/216#issuecomment-2550838333  

> Which symbol is missing?  
  
```  
  D:/M/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: libs/cobalt/CMakeFiles/boost_cobalt.dir/src/detail/util.cpp.obj:util.cpp:(.text+0x2a): undefined reference to `__imp_WSACleanup'  
  D:/M/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: libs/cobalt/CMakeFiles/boost_cobalt.dir/src/detail/util.cpp.obj:util.cpp:(.text+0x76): undefined reference to `__imp_WSAStartup'  
  D:/M/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: libs/cobalt/CMakeFiles/boost_cobalt.dir/src/channel.cpp.obj:channel.cpp:(.text+0x2a): undefined reference to `__imp_WSACleanup'  
  D:/M/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: libs/cobalt/CMakeFiles/boost_cobalt.dir/src/channel.cpp.obj:channel.cpp:(.text+0x76): undefined reference to `__imp_WSAStartup'  
  D:/M/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: libs/cobalt/CMakeFiles/boost_cobalt.dir/src/main.cpp.obj:main.cpp:(.text+0x46): undefined reference to `__imp_WSAStartup'  
  D:/M/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: libs/cobalt/CMakeFiles/boost_cobalt.dir/src/main.cpp.obj:main.cpp:(.text+0xda): undefined reference to `__imp_WSACleanup'  
  D:/M/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: libs/cobalt/CMakeFiles/boost_cobalt.dir/src/main.cpp.obj:main.cpp:(.text+0x1587): undefined reference to `__imp_WSAStartup'  
  D:/M/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: libs/cobalt/CMakeFiles/boost_cobalt.dir/src/main.cpp.obj:main.cpp:(.text+0x22dd): undefined reference to `__imp_WSACleanup'  
  D:/M/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: libs/cobalt/CMakeFiles/boost_cobalt.dir/src/main.cpp.obj:main.cpp:(.text+0x255c): undefined reference to `__imp_WSACleanup'  
  D:/M/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: libs/cobalt/CMakeFiles/boost_cobalt.dir/src/main.cpp.obj:main.cpp:(.text$_ZN5boost4asio10io_contextD1Ev[_ZN5boost4asio10io_contextD1Ev]+0xc2): undefined reference to `__imp_WSACleanup'  
  D:/M/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: libs/cobalt/CMakeFiles/boost_cobalt.dir/src/this_thread.cpp.obj:this_thread.cp:(.text+0x2a): undefined reference to `__imp_WSACleanup'  
  D:/M/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: libs/cobalt/CMakeFiles/boost_cobalt.dir/src/this_thread.cpp.obj:this_thread.cp:(.text+0x76): undefined reference to `__imp_WSAStartup'  
  D:/M/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: libs/cobalt/CMakeFiles/boost_cobalt.dir/src/thread.cpp.obj:thread.cpp:(.text+0x46): undefined reference to `__imp_WSAStartup'  
  D:/M/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: libs/cobalt/CMakeFiles/boost_cobalt.dir/src/thread.cpp.obj:thread.cpp:(.text+0xca): undefined reference to `__imp_WSACleanup'  
  D:/M/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: libs/cobalt/CMakeFiles/boost_cobalt.dir/src/thread.cpp.obj:thread.cpp:(.text+0xeef): undefined reference to `__imp_WSAStartup'  
  D:/M/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: libs/cobalt/CMakeFiles/boost_cobalt.dir/src/thread.cpp.obj:thread.cpp:(.text+0x1011): undefined reference to `__imp_WSACleanup'  
  D:/M/msys64/ucrt64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: libs/cobalt/CMakeFiles/boost_cobalt.dir/src/thread.cpp.obj:thread.cpp:(.text$_ZNSt23_Sp_counted_ptr_inplaceIN5boost6cobalt6detail12thread_stateESaIvELN9__gnu_cxx12_Lock_policyE2EE10_M_disposeEv[_ZNSt23_Sp_counted_ptr_inplaceIN5boost6cobalt6detail12thread_stateESaIvELN9__gnu_cxx12_Lock_policyE2EE10_M_disposeEv]+0x30a): undefined reference to `__imp_WSACleanup'  
```

---

## Comment 3

> Username: MehdiChinoune  
> Created_at: 2025-02-16 20:44:24 UTC  
> Url: https://github.com/boostorg/cobalt/pull/216#issuecomment-2661609949  

ping!

---

## Comment 4

> Username: MehdiChinoune  
> Created_at: 2025-04-19 04:13:53 UTC  
> Url: https://github.com/boostorg/cobalt/pull/216#issuecomment-2816513232  

Ping!

---
