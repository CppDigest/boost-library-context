# #54 - UWP support [Closed]

> Username: Kojoley  
> Created at: 2021-12-14 14:09:13 UTC  
> Updated at: 2021-12-14 23:25:25 UTC  
> Closed at: 2021-12-14 23:08:44 UTC  
> Url: https://github.com/boostorg/atomic/issues/54  

The library cannot be built for UWP because there is no `boost::winapi::get_module_handle` (`::GetModuleHandle`)  
  
```  
compile-c-c++ D:\buildtrees\boost-atomic\x64-uwp-dbg\boost\build\9357143429b3339f991610d5cdc76bfc\wait_on_address.obj  
  
    call "D:\buildtrees\boost-atomic\x64-uwp-dbg\boost\standalone\msvc\6b07b0b0f8e08318738a96bc606093b3\msvc-setup.bat" amd64 >nul  
 cl "..\src\wait_on_address.cpp" -c -Fo"D:\buildtrees\boost-atomic\x64-uwp-dbg\boost\build\9357143429b3339f991610d5cdc76bfc\wait_on_address.obj"     -TP /wd4675 /EHs /GR /Zc:throwingNew /nologo /DWIN32 /D_WINDOWS /W3 /utf-8 /GR /EHsc /MP /D_DEBUG /MDd /Z7 /Ob0 /Od /RTC1 /ZW /Z7 /Od /Ob0 /W3 /MDd /AI"C:\Program Files (x86)\Microsoft SDKs\Windows Kits\10\ExtensionSDKs\Microsoft.VCLibs\14.0\References\CommonConfiguration\neutral" /AI"C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.29.30133\lib\x86\store\references" /AI"C:\Program Files (x86)\Windows Kits\10\UnionMetadata\10.0.19041.0" /AI"C:\Program Files (x86)\Windows Kits\10\References\10.0.19041.0" /AI"C:\Windows\Microsoft.NET\Framework64\v4.0.30319" /D_WIN32_WINNT=0x0A00 /Zc:forScope /Zc:wchar_t /Zc:inline /favor:blend -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_ATOMIC_SOURCE -DBOOST_ATOMIC_USE_SSE2 -DBOOST_ATOMIC_USE_SSE41 -DBOOST_USE_WINDOWS_H -DWINAPI_FAMILY=WINAPI_FAMILY_APP "-I..\include" "-ID:\installed\x64-uwp\include" "-I..\src"   
  
wait_on_address.cpp  
..\src\wait_on_address.cpp(60): error C2039: 'get_module_handle': is not a member of 'boost::winapi'  
D:\installed\x64-uwp\include\boost/winapi/dll.hpp(190): note: see declaration of 'boost::winapi'  
..\src\wait_on_address.cpp(60): error C3861: 'get_module_handle': identifier not found  
..\src\wait_on_address.cpp(63): error C2039: 'get_proc_address': is not a member of 'boost::winapi'  
D:\installed\x64-uwp\include\boost/winapi/dll.hpp(190): note: see declaration of 'boost::winapi'  
..\src\wait_on_address.cpp(63): error C3861: 'get_proc_address': identifier not found  
..\src\wait_on_address.cpp(66): error C2039: 'get_proc_address': is not a member of 'boost::winapi'  
D:\installed\x64-uwp\include\boost/winapi/dll.hpp(190): note: see declaration of 'boost::winapi'  
..\src\wait_on_address.cpp(66): error C3861: 'get_proc_address': identifier not found  
..\src\wait_on_address.cpp(67): error C2039: 'get_proc_address': is not a member of 'boost::winapi'  
D:\installed\x64-uwp\include\boost/winapi/dll.hpp(190): note: see declaration of 'boost::winapi'  
..\src\wait_on_address.cpp(67): error C3861: 'get_proc_address': identifier not found  
compile-c-c++ D:\buildtrees\boost-atomic\x64-uwp-dbg\boost\build\9357143429b3339f991610d5cdc76bfc\lock_pool.obj  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-12-14 14:18:35 UTC  
> Url: https://github.com/boostorg/atomic/issues/54#issuecomment-993587461  

Most likely was broken by https://github.com/boostorg/atomic/commit/805e309a29e7bbbac01126884f368a1e56d669d3

---

## Comment 2

> Username: Lastique  
> Created at: 2021-12-14 23:08:57 UTC  
> Url: https://github.com/boostorg/atomic/issues/54#issuecomment-994124968  

Thanks for the report.

---

## Comment 3

> Username: Kojoley  
> Created at: 2021-12-14 23:25:25 UTC  
> Url: https://github.com/boostorg/atomic/issues/54#issuecomment-994134300  

Thanks for the quick fix.
