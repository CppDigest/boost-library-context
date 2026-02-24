# #967 - Trouble with linking in debug build [Open]

> Username: OMRKiruha  
> Created at: 2024-10-19 18:39:04 UTC  
> Updated at: 2024-10-19 18:39:04 UTC  
> Url: https://github.com/boostorg/boost/issues/967  

This [project](https://github.com/OMRKiruha/linkingBoostIssue) illustrate issue with liking Boost in Debug build. In Release it's all Ok. Used environment: Windows10 + MSYS2 + MinGW64   
GCC 14.2.0   
Cmake 3.30.4   
Boost 1.86.0   
Boost installed with: ./b2 --build-type=complete toolset=gcc threading=multi link=static runtime-link=static runtime-debugging=on --prefix=d:/boost install  
  
How I suppose difference between Debug and Release is only CMAKE flags: CMAKE_BUILD_TYPE=Debug or Release  
  
Linking error looks like this:  
```  
# cmake --build . -j 10  
[3/3] Linking CXX executable linkingBoostIssue.exe  
FAILED: linkingBoostIssue.exe  
C:\Windows\system32\cmd.exe /C "cd . && C:\msys64\mingw64\bin\c++.exe -Wall -Wextra -pedantic -static-libstdc++ -static-libgcc CMakeFiles/linkingBoostIssue.dir/main.cpp.obj CMakeFiles/linkingBoostIssue.dir/download.cpp.obj -o linkingBoostIssue.exe -Wl,--out-implib,liblinkingBoostIssue.dll.a -Wl,--major-image-version,0,--minor-image-version,0  C:/msys64/mingw64/lib/libssl.dll.a  C:/msys64/mingw64/lib/libcrypto.dll.a  -lpthread  -lwsock32  -lws2_32  C:/msys64/usr/lib/libdl.a  D:/boost/lib/libboost_system-mgw14-mt-sd-x64-1_86.a  D:/boost/lib/libboost_context-mgw14-mt-sd-x64-1_86.a  
  D:/boost/lib/libboost_system-mgw14-mt-sd-x64-1_86.a  D:/boost/lib/libboost_coroutine-mgw14-mt-sd-x64-1_86.a  D:/boost/lib/libboost_context-mgw14-mt-sd-x64-1_86.a  D:/boost/lib/libboost_date_time-mgw14-mt-sd-x64-1_86.a  D:/boost/lib/libboost_container-mgw14-mt-sd-x64-1_86.a  -lkernel32 -lus  
er32 -lgdi32 -lwinspool -lshell32 -lole32 -loleaut32 -luuid -lcomdlg32 -ladvapi32 && cd ."  
C:/msys64/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: CMakeFiles/linkingBoostIssue.dir/download.cpp.obj:download.cpp:(.text+0x6dba): undefined reference to `boost::exception_detail::exception_ptr_static_exception_object<boost::exception_detail::bad_alloc_>::e'  
C:/msys64/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/14.2.0/../../../../x86_64-w64-mingw32/bin/ld.exe: CMakeFiles/linkingBoostIssue.dir/download.cpp.obj:download.cpp:(.text+0x6dd8): undefined reference to `boost::exception_detail::exception_ptr_static_exception_object<boost::exception_detail::bad_exception_>::e'  
```  
  
And lot of 'undefined reference'
