# #385 - MSVC compile error [Open]

> Username: OgreTransporter  
> Created at: 2024-07-12 14:57:52 UTC  
> Updated at: 2025-12-17 15:20:35 UTC  
> Url: https://github.com/boostorg/process/issues/385  

I tried to build boost as in the documentation with b2. However, I get the following error message:  
```  
16:44:14 compile-c-c++ build-143-x64\boost\bin.v2\libs\process\build\msvc-14.3\debug\x86_64\threading-multi\shell.obj  
16:44:14 shell.cpp  
16:44:14 Please define _WIN32_WINNT or _WIN32_WINDOWS appropriately. For example:  
16:44:14 - add -D_WIN32_WINNT=0x0601 to the compiler command line; or  
16:44:14 - add _WIN32_WINNT=0x0601 to your project's Preprocessor Definitions.  
16:44:14 Assuming _WIN32_WINNT=0x0601 (i.e. Windows 7 target).  
16:44:14 msvc.link.dll build-143-x64\boost\bin.v2\libs\process\build\msvc-14.3\debug\x86_64\threading-multi\boost_process-vc143-mt-gd-x64-1_86.dll  
16:44:14 process_handle_windows.obj : error LNK2019: Unresolved external symbol "RtlNtStatusToDosError" in function ""void __cdecl boost::process::v2::detail::suspend_(void *,class boost::system::error_code &)" (?suspend_@detail@v2@process@boost@@YAXPEAXAEAVerror_code@system@4@@Z)".  
16:44:14 env.obj : error LNK2001: Unresolved external symbol "RtlNtStatusToDosError".  
16:44:14 proc_info.obj : error LNK2001: Unresolved external symbol "RtlNtStatusToDosError".  
16:44:14 process_handle_windows.obj : error LNK2019: Unresolved external symbol "NtResumeProcess" in function ""void __cdecl boost::process::v2::detail::resume_(void *,class boost::system::error_code &)" (?resume_@detail@v2@process@boost@@YAXPEAXAEAVerror_code@system@4@@Z)".  
16:44:14 process_handle_windows.obj : error LNK2019: Unresolved external symbol "NtSuspendProcess" in function ""void __cdecl boost::process::v2::detail::suspend_(void *,class boost::system::error_code &)" (?suspend_@detail@v2@process@boost@@YAXPEAXAEAVerror_code@system@4@@Z)".  
16:44:14 env.obj : error LNK2019: Unresolved external symbol "NtQueryInformationProcess" in function ""struct boost::process::v2::ext::env_view __cdecl boost::process::v2::ext::env(void *,class boost::system::error_code &)" (?env@ext@v2@process@boost@@YA?AUenv_view@1234@PEAXAEAVerror_code@system@4@@Z)".  
16:44:14 proc_info.obj : error LNK2001: Unresolved external symbol "NtQueryInformationProcess".  
16:44:14 build-143-x64\boost\bin.v2\libs\process\build\msvc-14.3\debug\x86_64\threading-multi\boost_process-vc143-mt-gd-x64-1_86.dll : fatal error LNK1120: 4 unresolved external links  
```  
  
 - Windows 10  
 - Visual Studio 2022  
 - 2c372461e8ab49fde18bff6c3c5e47b721d70383

---

## Comment 1

> Username: OgreTransporter  
> Created at: 2024-07-15 06:32:33 UTC  
> Url: https://github.com/boostorg/process/issues/385#issuecomment-2227785158  

Fixed by 8b3e90234b7eb6ab06e32b6da4fb17eb09d42571

---

## Comment 2

> Username: OgreTransporter  
> Created at: 2024-07-15 07:20:54 UTC  
> Updated at: 2024-07-15 07:24:04 UTC  
> Url: https://github.com/boostorg/process/issues/385#issuecomment-2227846326  

New error:  
```  
...failed updating 2 targets...  
   common.copy D:\workspace\boost\install\lib\boost_process-vc143-mt-gd-x64-1_86.lib  
   common.copy D:\workspace\boost\install\lib\boost_process-vc143-mt-x64-1_86.lib  
```  
  
Edit:  
```  
common.copy D:\workspace\boost\install\lib\boost_process-vc143-mt-gd-x64-1_86.lib  
The system cannot find the specified file.  
  
     copy /b "build-143-x64\boost\bin.v2\libs\process\build\msvc-14.3\debug\x86_64\threading-multi\boost_process-vc143-mt-gd-x64-1_86.lib" + this-file-does-not-exist-A698EE7806899E69 "D:\workspace\boost\install\lib\boost_process-vc143-mt-gd-x64-1_86.lib"  
  
 ...failed common.copy D:\workspace\boost\install\lib\boost_process-vc143-mt-gd-x64-1_86.lib...  
```

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-07-19 01:06:08 UTC  
> Url: https://github.com/boostorg/process/issues/385#issuecomment-2237831662  

Can you share the commands used for the last error?

---

## Comment 4

> Username: OgreTransporter  
> Created at: 2024-07-21 11:46:05 UTC  
> Url: https://github.com/boostorg/process/issues/385#issuecomment-2241580078  

```  
cd /d D:\workspace\boost  
SET WORKSPACE=%CD%  
SET PATH=D:\VisualStudio\Tools\Git\bin;D:\VisualStudio\Tools\Python\3.11.9;%PATH%  
git clone https://github.com/madler/zlib.git %CD%\deps\zlib\src  
git clone https://git.code.sf.net/p/bzip2/bzip2 %CD%\deps\bzip2\src  
git clone --recursive https://github.com/boostorg/boost.git %CD%\src  
powershell -ExecutionPolicy ByPass -C "if((Get-Content -Path ($env:WORKSPACE+'\src\libs\process\build\Jamfile') -Raw).IndexOf('lib shell32') -lt 0) { Start-BitsTransfer -Dynamic -Source 'https://raw.githubusercontent.com/boostorg/process/develop/build/Jamfile' -Destination ($env:WORKSPACE+'\src\libs\process\build\Jamfile') }"  
echo using python : 3.11 : D:\VisualStudio\Tools\Python\3.11.9\python.exe>%CD%\src\user-config.jam  
echo     : D:\VisualStudio\Tools\Python\3.11.9\include>>%CD%\src\user-config.jam  
echo     : D:\VisualStudio\Tools\Python\3.11.9\libs ;>>%CD%\src\user-config.jam  
powershell -ExecutionPolicy ByPass -C "(Get-Content -Path ($env:WORKSPACE+'\src\user-config.jam') -Raw).Replace('\','\\') | Set-Content -Path ($env:WORKSPACE+'\src\user-config.jam')"  
cd src  
call bootstrap.bat  
b2.exe headers --user-config=%CD%\user-config.jam  
b2.exe --user-config=%CD%\user-config.jam -j %NUMBER_OF_PROCESSORS% --toolset=msvc-14.3 --build-type=complete --build-dir=build-143-x64 -sBZIP2_SOURCE=%WORKSPACE%\deps\bzip2\src -sBZIP2_BINARY=bzip2static -sBZIP2_INCLUDE=%WORKSPACE%\deps\bzip2\install\include -sBZIP2_LIBPATH=%WORKSPACE%\deps\bzip2\install\lib -sZLIB_SOURCE=%WORKSPACE%\deps\zlib\src -sZLIB_BINARY=zlibstatic -sZLIB_INCLUDE=%WORKSPACE%\deps\zlib\install\include -sZLIB_LIBPATH=%WORKSPACE%\deps\zlib\install\lib architecture=x86 address-model=64 threading=multi stage --stagedir=%WORKSPACE%\install  
```  
  
At the end it says:  
```  
...updated 6078 targets...  
  
...failed updating 2 targets...  
   common.copy D:\workspace\boost\install\lib\boost_process-vc143-mt-gd-x64-1_86.lib  
   common.copy D:\workspace\boost\install\lib\boost_process-vc143-mt-x64-1_86.lib  
```  
  
Unfortunately, there is not much in the build log:  
[build.log](https://github.com/user-attachments/files/16324064/build.log)  
  
```  
msvc.link.dll build-143-x64\boost\bin.v2\libs\process\build\msvc-14.3\debug\x86_64\threading-multi\boost_process-vc143-mt-gd-x64-1_86.dll  
common.copy D:\workspace\boost\install\lib\boost_process-vc143-mt-gd-x64-1_86.lib  
Das System kann die angegebene Datei nicht finden.  
  
    copy /b "build-143-x64\boost\bin.v2\libs\process\build\msvc-14.3\debug\x86_64\threading-multi\boost_process-vc143-mt-gd-x64-1_86.lib" + this-file-does-not-exist-A698EE7806899E69 "D:\workspace\boost\install\lib\boost_process-vc143-mt-gd-x64-1_86.lib"  
  
...failed common.copy D:\workspace\boost\install\lib\boost_process-vc143-mt-gd-x64-1_86.lib...  
```

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2024-07-22 00:15:17 UTC  
> Url: https://github.com/boostorg/process/issues/385#issuecomment-2241831939  

I have no idea what's going on. Maybe try to remove bin.v2 and build from scratch?

---

## Comment 6

> Username: atomictoquark  
> Created at: 2025-03-26 13:16:11 UTC  
> Url: https://github.com/boostorg/process/issues/385#issuecomment-2754368749  

this build error is because of use undocument windows API ,Ntxxxx(NtQueryInformationProcess etc.),on windows MSCV,its need   
#pragma comment(lib,"ntdll.lib")

---

## Comment 7

> Username: jens-diewald  
> Created at: 2025-12-17 15:20:34 UTC  
> Url: https://github.com/boostorg/process/issues/385#issuecomment-3665835630  

Not exactly the same, but probably related:  
I built Boost 1.90 with b2 and MSVC on windows 11. When I try to compile a basic program containing `auto proc = process(ctx, path, {});`, linking boost statically,  I get a linker error:  
```  
1>libboost_process-vc143-mt-x64-1_90.lib(process_handle_windows.obj) : error LNK2001: unresolved external symbol RtlNtStatusToDosError  
1>libboost_process-vc143-mt-x64-1_90.lib(process_handle_windows.obj) : error LNK2001: unresolved external symbol NtResumeProcess  
1>libboost_process-vc143-mt-x64-1_90.lib(process_handle_windows.obj) : error LNK2001: unresolved external symbol NtSuspendProcess  
```  
As stated above, this can be fixed by linking against `ntdll.lib`.
