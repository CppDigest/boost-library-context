# #440 CMake: Fix `MinGW` missing winsock `ws2_32` lib linking [Open]

> Username: Challanger524  
> Created at: 2025-01-30 11:57:00 UTC  
> Updated at: 2025-02-21 14:48:55 UTC  
> Url: https://github.com/boostorg/asio/pull/440  

PR for: https://github.com/chriskohlhoff/asio/issues/1590  
  
## MSYS2 MinGW not linking ws2_32: undefined reference to `__imp_WSAStartup'  
  
This issue is old as world.  
  
MSYS2/mingw64/GCC: **undefined reference to `__imp_WSAStartup'**  
```  
[build] C:\windows\system32\cmd.exe /C "cd . && C:\msys64\mingw64\bin\g++.exe -g  CMakeFiles/test-beast.dir/src/main.cpp.obj -o C:\*\dev\test-boost-beast\test-beast.exe -Wl,--out-implib,libtest-beast.dll.a -Wl,--major-image-version,0,--minor-image-version,0  dep/boost/libs/context/libboost_context-gcc14-mt-d-x64-1_88.a  dep/boost/libs/date_time/libboost_date_time-gcc14-mt-d-x64-1_88.a  dep/boost/libs/container/libboost_container-gcc14-mt-d-x64-1_88.a  -lkernel32 -luser32 -lgdi32 -lwinspool -lshell32 -lole32 -loleaut32 -luuid -lcomdlg32 -ladvapi32 && cd ."  
[build] C:/msys64/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: CMakeFiles/test-beast.dir/src/main.cpp.obj: in function `boost::asio::detail::winsock_init_base::startup(boost::asio::detail::winsock_init_base::data&, unsigned char, unsigned char)':  
[build] C:/*/dev/boost/libs/asio/include/boost/asio/detail/impl/winsock_init.ipp:39:(.text$_ZN5boost4asio6detail17winsock_init_base7startupERNS2_4dataEhh[_ZN5boost4asio6detail17winsock_init_base7startupERNS2_4dataEhh]+0x36): undefined reference to `__imp_WSAStartup'  
```  
  
MSYS2/clang64/Clang: ld.lld: error: **undefined symbol: __declspec(dllimport) WSAStartup**  
```  
[build] C:\windows\system32\cmd.exe /C "cd . && C:\msys64\clang64\bin\clang++.exe -g  CMakeFiles/test-beast.dir/src/main.cpp.obj -o C:\*\dev\test-boost-beast\test-beast.exe -Wl,--out-implib,libtest-beast.dll.a -Wl,--major-image-version,0,--minor-image-version,0  dep/boost/libs/context/libboost_context-clang19-mt-d-x64-1_88.a  dep/boost/libs/date_time/libboost_date_time-clang19-mt-d-x64-1_88.a  dep/boost/libs/container/libboost_container-clang19-mt-d-x64-1_88.a  -lkernel32 -luser32 -lgdi32 -lwinspool -lshell32 -lole32 -loleaut32 -luuid -lcomdlg32 -ladvapi32 && cd ."  
[build] ld.lld: error: undefined symbol: __declspec(dllimport) WSAStartup  
[build] >>> referenced by C:/*/dev/boost/libs/asio/include/boost/asio/detail/impl/winsock_init.ipp:39  
[build] >>>               CMakeFiles/test-beast.dir/src/main.cpp.obj:(boost::asio::detail::winsock_init_base::startup(boost::asio::detail::winsock_init_base::data&, unsigned char, unsigned char))  
```  
  
## Solution / workaround  
Simply link library `ws2_32` to your or asio project.  
  
```cmake  
if (MINGW)  
  target_link_libraries(${PROJECT_NAME} PRIVATE ws2_32) # mswsock wsock32  
endif()  
````  
or  
``` cmake  
if (MINGW)  
  target_link_libraries(boost_asio INTERFACE ws2_32) # mswsock wsock32  
endif()  
```  
  
## Credits  
try to build cmake+mingw+win10/64bit problem         : https://github.com/boostorg/beast/issues/672#issuecomment-318225620  
Installing boost libraries for GCC (MinGW) on Windows: https://gist.github.com/Shivam60/4a3a4923616ed317a9a094a8173c3391  
  
## Asio solution  
  
As I understand - Asio author uses C++ source files to handle library includes _(I have no words)_: https://github.com/chriskohlhoff/asio/blob/62481a25be6cf78cbe714419a4462fd89bd84ab9/asio/include/asio/detail/socket_types.hpp#L47-L53

---
