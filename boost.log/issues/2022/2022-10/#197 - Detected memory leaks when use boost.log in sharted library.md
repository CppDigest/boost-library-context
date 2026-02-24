# #197 - Detected memory leaks when use boost.log in sharted library. [Closed]

> Username: zaazbb  
> Created at: 2022-10-12 06:24:00 UTC  
> Updated at: 2022-10-13 05:00:40 UTC  
> Closed at: 2022-10-13 05:00:40 UTC  
> Url: https://github.com/boostorg/log/issues/197  

Link boost.log into dll on windows,  and load it in MFC project, Detect memory leaks by visual studio.  
  
boost: 1.80.0  
os: windows 11  
  
Here is the sharted library project in Clion.  
CMakeLists.txt  
```  
cmake_minimum_required(VERSION 3.23)  
project(untitled1)  
  
set(CMAKE_CXX_STANDARD 14)  
  
add_library(untitled1 SHARED library.cpp)  
  
find_package(Boost REQUIRED COMPONENTS log)  
if(Boost_FOUND)  
    target_include_directories(${PROJECT_NAME} PRIVATE ${Boost_INCLUDE_DIRS})  
    target_link_directories(${PROJECT_NAME} PRIVATE ${Boost_LIBRARY_DIRS})  
    target_link_libraries(${PROJECT_NAME} PRIVATE ${Boost_LIBRARIES})  
endif()  
```  
library.h  
``` cpp  
#ifndef UNTITLED1_LIBRARY_H  
#define UNTITLED1_LIBRARY_H  
  
__declspec(dllexport) void __stdcall hello();  
  
#endif //UNTITLED1_LIBRARY_H  
```  
  
library.cpp  
``` cpp  
#include "library.h"  
  
#include <iostream>  
  
__declspec(dllexport) void __stdcall hello() {  
  std::cout << "Hello, World!" << std::endl;  
}  
```  
  
add "--verbose" option to cmake build, we can see the raw build commonds.  
```  
====================[ Build | untitled1 | Debug ]===============================  
"C:\Program Files\JetBrains\CLion 2022.1.1\bin\cmake\win\bin\cmake.exe" --build E:\test\cpp\boost\untitled1\cmake-build-debug --target untitled1 --verbose  
[0/1] "C:\Program Files\JetBrains\CLion 2022.1.1\bin\cmake\win\bin\cmake.exe" --regenerate-during-build -SE:\test\cpp\boost\untitled1 -BE:\test\cpp\boost\untitled1\cmake-build-debug  
-- Configuring done  
-- Generating done  
-- Build files have been written to: E:/test/cpp/boost/untitled1/cmake-build-debug  
[1/2] C:\PROGRA~2\MICROS~3\2019\ENTERP~1\VC\Tools\MSVC\1429~1.301\bin\Hostx86\x86\cl.exe  /nologo /TP -DBOOST_ATOMIC_NO_LIB -DBOOST_CHRONO_NO_LIB -DBOOST_FILESYSTEM_NO_LIB -DBOOST_LOG_NO_LIB -DBOOST_REGEX_NO_LIB -DBOOST_THREAD_NO_LIB -Duntitled1_EXPORTS -external:IE:\soft\boost_1_80_0_vc_x86\boost_install\include\boost-1_80 -external:W0 /DWIN32 /D_WINDOWS /EHsc /Zi /Ob0 /Od /RTC1 -MDd /showIncludes /FoCMakeFiles\untitled1.dir\library.cpp.obj /FdCMakeFiles\untitled1.dir\ /FS -c E:\test\cpp\boost\untitled1\library.cpp  
[2/2] cmd.exe /C "cd . && "C:\Program Files\JetBrains\CLion 2022.1.1\bin\cmake\win\bin\cmake.exe" -E vs_link_dll --intdir=CMakeFiles\untitled1.dir --rc=C:\PROGRA~2\WI3CF2~1\10\bin\100190~1.0\x86\rc.exe --mt=C:\PROGRA~2\WI3CF2~1\10\bin\100190~1.0\x86\mt.exe --manifests  -- C:\PROGRA~2\MICROS~3\2019\ENTERP~1\VC\Tools\MSVC\1429~1.301\bin\Hostx86\x86\link.exe /nologo CMakeFiles\untitled1.dir\library.cpp.obj  /out:untitled1.dll /implib:untitled1.lib /pdb:untitled1.pdb /dll /version:0.0 /machine:X86 /debug /INCREMENTAL -LIBPATH:E:\soft\boost_1_80_0_vc_x86\boost_install\lib E:\soft\boost_1_80_0_vc_x86\boost_install\lib\libboost_log-vc142-mt-gd-x32-1_80.lib  advapi32.lib  mswsock.lib  psapi.lib  secur32.lib  ws2_32.lib  advapi32.lib  mswsock.lib  psapi.lib  secur32.lib  ws2_32.lib  E:\soft\boost_1_80_0_vc_x86\boost_install\lib\libboost_filesystem-vc142-mt-gd-x32-1_80.lib  bcrypt.lib  synchronization.lib  bcrypt.lib  synchronization.lib  E:\soft\boost_1_80_0_vc_x86\boost_install\lib\libboost_atomic-vc142-mt-gd-x32-1_80.lib  E:\soft\boost_1_80_0_vc_x86\boost_install\lib\libboost_regex-vc142-mt-gd-x32-1_80.lib  E:\soft\boost_1_80_0_vc_x86\boost_install\lib\libboost_thread-vc142-mt-gd-x32-1_80.lib  E:\soft\boost_1_80_0_vc_x86\boost_install\lib\libboost_chrono-vc142-mt-gd-x32-1_80.lib  kernel32.lib user32.lib gdi32.lib winspool.lib shell32.lib ole32.lib oleaut32.lib uuid.lib comdlg32.lib advapi32.lib  && cd ."  
  
Build finished  
```  
  
in MFC project, call hello() in a button callback to ensure dll can be loaded.  
``` cpp  
void CMFCApplication1Dlg::OnBnClickedButton1()  
{  
	// TODO: Add your control notification handler code here  
	hello();  
}  
```  
run the MFC dialog, and then close it(do not click the button, so the hello() not be called).  
when the dialog exit, report some memory leaks by visual studio as below.  
  
```  
'MFCApplication1.exe' (Win32): Loaded 'C:\Users\zhang\source\repos\MFCApplication1\Debug\MFCApplication1.exe'. Symbols loaded.  
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\ntdll.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\kernel32.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\KernelBase.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\user32.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\win32u.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\mfc140ud.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\gdi32.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\advapi32.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\gdi32full.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\msvcp_win.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\msvcrt.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\sechost.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\ucrtbase.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\rpcrt4.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\oleaut32.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'E:\test\cpp\boost\untitled1\cmake-build-debug\untitled1.dll'. Symbols loaded.  
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\ole32.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\combase.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\WinSxS\x86_microsoft.windows.common-controls_6595b64144ccf1df_6.0.22621.608_none_f0f1837ed587d923\comctl32.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\shlwapi.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\imm32.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\vcruntime140d.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\ucrtbased.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\WinSxS\x86_microsoft.windows.gdiplus_6595b64144ccf1df_1.1.22621.608_none_21dc7664301a52f1\GdiPlus.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\uxtheme.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\msvcp140d.dll'.   
'MFCApplication1.exe' (Win32): Unloaded 'C:\Windows\SysWOW64\msvcp140d.dll'  
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\msvcp140d.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\bcryptprimitives.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\shell32.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\msctf.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\kernel.appcore.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\TextInputFramework.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\TextShaping.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\CoreMessaging.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\CoreUIComponents.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\WinTypes.dll'.   
'MFCApplication1.exe' (Win32): Loaded 'C:\Windows\SysWOW64\cryptbase.dll'.   
The thread 0xc620 has exited with code 2 (0x2).  
The thread 0xd580 has exited with code 2 (0x2).  
The thread 0xc90c has exited with code 2 (0x2).  
The thread 0x7b34 has exited with code 2 (0x2).  
The thread 0xcd2c has exited with code 2 (0x2).  
Detected memory leaks!  
Dumping objects ->  
{149} normal block at 0x00959B50, 16 bytes long.  
 Data: <   z            > B4 97 93 7A 02 00 00 00 01 00 00 00 F0 9A 95 00   
{148} normal block at 0x00959AF0, 48 bytes long.  
 Data: <   z     k z k z> 08 97 93 7A 00 00 00 00 D0 6B 93 7A 88 6B 93 7A   
{147} normal block at 0x00958048, 16 bytes long.  
 Data: <   z            > 90 97 93 7A 02 00 00 00 01 00 00 00 90 9A 95 00   
{146} normal block at 0x00959A90, 48 bytes long.  
 Data: <   z     j z j z> CC 96 93 7A 00 00 00 00 F8 6A 93 7A B0 6A 93 7A   
Object dump complete.  
The program '[0xC958] MFCApplication1.exe' has exited with code 2 (0x2).  
```  
  
Please tell me the right way to remove those memory leaks.   
Thank you!.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-10-12 08:00:45 UTC  
> Url: https://github.com/boostorg/log/issues/197#issuecomment-1275752071  

The output doesn't tell where those allocations were made, what makes you think they were made by Boost.Log? Can you reduce your test case to only use Boost.Log and no other libraries? Did you use any memory debugging software?

---

## Comment 2

> Username: zaazbb  
> Created at: 2022-10-13 04:19:56 UTC  
> Updated at: 2022-10-13 04:32:16 UTC  
> Url: https://github.com/boostorg/log/issues/197#issuecomment-1277007552  

If i comment the boost lines in cmakelists.txt, no memory leak detected.  
```  
#find_package(Boost REQUIRED COMPONENTS log)  
#if(Boost_FOUND)  
#    target_include_directories(${PROJECT_NAME} PRIVATE ${Boost_INCLUDE_DIRS})  
#    target_link_directories(${PROJECT_NAME} PRIVATE ${Boost_LIBRARY_DIRS})  
#    target_link_libraries(${PROJECT_NAME} PRIVATE ${Boost_LIBRARIES})  
#endif()  
```  
i use visual studio ide, it  can find memory leak with CRT library. (https://learn.microsoft.com/en-us/visualstudio/debugger/finding-memory-leaks-using-the-crt-library?view=vs-2022)  
i don't known which other memory debug tools  on windows can be use.  
  
of course, it maybe fake memory leaks. because if you add a global stl container in shared library, (without boost library in cmakelists.txt), the visualstudio also detect a memory leak.    
  
```  
#include "library.h"  
  
#include <iostream>  
  
// add this line, visual studio also detect memory leaks.  
std::map<int, int> m;  
  
__declspec(dllexport) void __stdcall hello() {  
  std::cout << "Hello, World!" << std::endl;  
}  
```  
does the boost.log allocate some data in global scrope, make vc notify a fake momory leak?

---

## Comment 3

> Username: zaazbb  
> Created at: 2022-10-13 05:00:40 UTC  
> Url: https://github.com/boostorg/log/issues/197#issuecomment-1277033760  

i config the dll delay loaded, no memory leak appear anymore.  
https://docs.adaptive-vision.com/4.8/avl/usage_tips/MemoryLeakDetection.html
