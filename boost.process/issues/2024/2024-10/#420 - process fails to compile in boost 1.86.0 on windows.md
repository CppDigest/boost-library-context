# #420 - process fails to compile in boost 1.86.0 on windows [Open]

> Username: tomy2105  
> Created at: 2024-10-30 15:11:34 UTC  
> Updated at: 2024-11-05 14:41:28 UTC  
> Url: https://github.com/boostorg/process/issues/420  

I'm compiling boost on windows and boost process fails to compile.  
  
Compilation done by following commands in boost src folder.  
```  
bootstrap.bat  
b2.exe -d4 --build-type=minimal stage variant=release address-model=32 link=shared threading=multi runtime-link=shared debug-symbols=on debug-store=database -j%NUMBER_OF_PROCESSORS% --layout=  
system --with-process --build-dir=..\boost_build --stagedir=..\boost_stage\ %XP_TOOLSET_DEFINES% > bjam.log 2>&1  
```  
  
I've used `--with-process` just to cut down compile time and log to reproduce problem, same happens if this flag is not used.  
After this is done several libs are compiled in `..\boost_stage\lib` (atomic and filesystem) but process is not.  
  
Log file is attached [bjam.log.zip](https://github.com/user-attachments/files/17575091/bjam.log.zip), I think the root cause are those compilation errors:  
```  
Executing using a command file and the shell: cmd.exe /Q/C  
Command string for CreateProcessA(): 'cmd.exe /Q/C "C:\Users\Admin\AppData\Local\Temp\jam15820-05-00.bat"'  
compile-c-c++ ..\boost_build\boost\bin.v2\libs\process\build\msvc-14.3\release\x86_32\debug-store-database\debug-symbols-on\threading-multi\ext\exe.obj  
  
    call "..\boost_build\boost\bin.v2\standalone\msvc\msvc-14.3\address-model-32\architecture-x86\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo "libs\process\src\ext\exe.cpp" -c -Fo"..\boost_build\boost\bin.v2\libs\process\build\msvc-14.3\release\x86_32\debug-store-database\debug-symbols-on\threading-multi\ext\exe.obj" /Fd"..\boost_build\boost\bin.v2\libs\process\build\msvc-14.3\release\x86_32\debug-store-database\debug-symbols-on\threading-multi\ext\exe.pdb"    -TP /wd4675 /EHs /GR /Zc:throwingNew /O2 /Zi /Ob2 /W3 /MD /Zc:forScope /Zc:wchar_t /Zc:inline /Gw /bigobj -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_PROCESS_DYN_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DNDEBUG -DNTDDI_VERSION=0x05010000 -DPSAPI_VERSION=1 -DWIN32_LEAN_AND_MEAN -DWINVER=0x0501 -D_CRT_SECURE_NO_DEPRECATE -D_SCL_SECURE_NO_WARNINGS -D_WIN32_WINNT=0x0501 "-I."   
  
exe.cpp  
libs\process\src\ext\exe.cpp(78): error C3861: 'QueryFullProcessImageNameW': identifier not found  
0.125000 sec system; 0.031250 sec user; 3.299207 sec clock  
...failed compile-c-c++ ..\boost_build\boost\bin.v2\libs\process\build\msvc-14.3\release\x86_32\debug-store-database\debug-symbols-on\threading-multi\ext\exe.obj...  
```  
  
```  
Command string for CreateProcessA(): 'cmd.exe /Q/C "C:\Users\Admin\AppData\Local\Temp\jam15820-01-00.bat"'  
compile-c-c++ ..\boost_build\boost\bin.v2\libs\process\build\msvc-14.3\release\x86_32\debug-store-database\debug-symbols-on\threading-multi\windows\default_launcher.obj  
  
    call "..\boost_build\boost\bin.v2\standalone\msvc\msvc-14.3\address-model-32\architecture-x86\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo "libs\process\src\windows\default_launcher.cpp" -c -Fo"..\boost_build\boost\bin.v2\libs\process\build\msvc-14.3\release\x86_32\debug-store-database\debug-symbols-on\threading-multi\windows\default_launcher.obj" /Fd"..\boost_build\boost\bin.v2\libs\process\build\msvc-14.3\release\x86_32\debug-store-database\debug-symbols-on\threading-multi\windows\default_launcher.pdb"    -TP /wd4675 /EHs /GR /Zc:throwingNew /O2 /Zi /Ob2 /W3 /MD /Zc:forScope /Zc:wchar_t /Zc:inline /Gw /bigobj -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_PROCESS_DYN_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DNDEBUG -DNTDDI_VERSION=0x05010000 -DPSAPI_VERSION=1 -DWIN32_LEAN_AND_MEAN -DWINVER=0x0501 -D_CRT_SECURE_NO_DEPRECATE -D_SCL_SECURE_NO_WARNINGS -D_WIN32_WINNT=0x0501 "-I."   
  
default_launcher.cpp  
D:\Projects\boost\boost/process/v2/windows/default_launcher.hpp(220): error C3646: 'startup_info': unknown override specifier  
D:\Projects\boost\boost/process/v2/windows/default_launcher.hpp(220): error C2059: syntax error: '{'  
D:\Projects\boost\boost/process/v2/windows/default_launcher.hpp(220): error C2334: unexpected token(s) preceding '{'; skipping apparent function body  
0.078125 sec system; 0.046875 sec user; 3.474433 sec clock  
...failed compile-c-c++ ..\boost_build\boost\bin.v2\libs\process\build\msvc-14.3\release\x86_32\debug-store-database\debug-symbols-on\threading-multi\windows\default_launcher.obj...  
```  
  
```  
Command string for CreateProcessA(): 'cmd.exe /Q/C "C:\Users\Admin\AppData\Local\Temp\jam15820-04-00.bat"'  
compile-c-c++ ..\boost_build\boost\bin.v2\libs\process\build\msvc-14.3\release\x86_32\debug-store-database\debug-symbols-on\threading-multi\environment.obj  
  
    call "..\boost_build\boost\bin.v2\standalone\msvc\msvc-14.3\address-model-32\architecture-x86\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo "libs\process\src\environment.cpp" -c -Fo"..\boost_build\boost\bin.v2\libs\process\build\msvc-14.3\release\x86_32\debug-store-database\debug-symbols-on\threading-multi\environment.obj" /Fd"..\boost_build\boost\bin.v2\libs\process\build\msvc-14.3\release\x86_32\debug-store-database\debug-symbols-on\threading-multi\environment.pdb"    -TP /wd4675 /EHs /GR /Zc:throwingNew /O2 /Zi /Ob2 /W3 /MD /Zc:forScope /Zc:wchar_t /Zc:inline /Gw /bigobj -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_PROCESS_DYN_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DNDEBUG -DNTDDI_VERSION=0x05010000 -DPSAPI_VERSION=1 -DWIN32_LEAN_AND_MEAN -DWINVER=0x0501 -D_CRT_SECURE_NO_DEPRECATE -D_SCL_SECURE_NO_WARNINGS -D_WIN32_WINNT=0x0501 "-I."   
  
environment.cpp  
D:\Projects\boost\boost/process/v2/windows/default_launcher.hpp(220): error C3646: 'startup_info': unknown override specifier  
D:\Projects\boost\boost/process/v2/windows/default_launcher.hpp(220): error C2059: syntax error: '{'  
D:\Projects\boost\boost/process/v2/windows/default_launcher.hpp(220): error C2334: unexpected token(s) preceding '{'; skipping apparent function body  
0.093750 sec system; 0.031250 sec user; 3.934528 sec clock  
...failed compile-c-c++ ..\boost_build\boost\bin.v2\libs\process\build\msvc-14.3\release\x86_32\debug-store-database\debug-symbols-on\threading-multi\environment.obj...  
```  
  
How can this be fixed?  
  
Thank you very much in advance :).

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-10-30 15:25:52 UTC  
> Url: https://github.com/boostorg/process/issues/420#issuecomment-2447541757  

Which windows version are you compiling on and for? XP?

---

## Comment 2

> Username: tomy2105  
> Created at: 2024-10-30 15:33:43 UTC  
> Updated at: 2024-10-30 15:35:48 UTC  
> Url: https://github.com/boostorg/process/issues/420#issuecomment-2447566750  

Sorry forgot to mention, in b2 command there is `XP_TOOLSET_DEFINES` used.  
```  
SET XP_TOOLSET_DEFINES=define=WINVER=0x0501 define=_WIN32_WINNT=0x0501 define=NTDDI_VERSION=0x05010000 define=PSAPI_VERSION=1  
```  
  
Project I work for is a bit old, so these are the settings used so far, but if those are the cause of the problem they can be bumped up a bit (XP is no longer required to be supported)....  
Need support for Windows Server 2012 and newer and Windows 10 and 11.  
  
Otherwise compiled on Windows 10, SDK version (I have several) latest 10.0.22621.   
Visual Studio (and its toolset used) is 17.11.4.

---

## Comment 3

> Username: tomy2105  
> Created at: 2024-10-31 10:45:47 UTC  
> Url: https://github.com/boostorg/process/issues/420#issuecomment-2449555622  

You were absolutely right! Thank you very much for the nudge in right direction....  
  
When I set (now a bit of a misnomer):  
```  
SET XP_TOOLSET_DEFINES=define=WINVER=0x0600 define=_WIN32_WINNT=0x0600 define=NTDDI_VERSION=0x06000000 define=PSAPI_VERSION=1  
```  
Then process compiles as expected!  
  
My problem is solved and as far as I'm concerned this can be closed.  
  
However, since all other boost libs seem to compile with 0x0501, maybe process should try to do that too if possible.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2024-11-05 14:41:27 UTC  
> Url: https://github.com/boostorg/process/issues/420#issuecomment-2457358139  

I think those functions can yield an error_not_supported on windows. @time-killer-games do you think you can implement a check for that?
