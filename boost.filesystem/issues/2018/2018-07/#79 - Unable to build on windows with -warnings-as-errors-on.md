# #79 - Unable to build on windows with <warnings-as-errors>on [Closed]

> Username: jeking3  
> Created at: 2018-07-17 11:42:16 UTC  
> Updated at: 2018-11-24 17:55:30 UTC  
> Closed at: 2018-11-24 17:55:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/79  

While building Boost.Iostreams with warnings-as-errors enabled, I encountered these warnings:  
  
```  
compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.1\release\address-model-64\cxxstd-03-iso\link-static\threading-multi\unique_path.obj  
unique_path.cpp  
libs\filesystem\src\unique_path.cpp(114): error C2220: warning treated as error - no 'object' file generated  
libs\filesystem\src\unique_path.cpp(114): warning C4267: 'argument': conversion from 'size_t' to 'DWORD', possible loss of data  
    call "bin.v2\standalone\msvc\msvc-14.1\address-model-64\architecture-x86\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"bin.v2\libs\filesystem\build\msvc-14.1\release\address-model-64\cxxstd-03-iso\link-static\threading-multi\unique_path.obj.rsp"   
...failed compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.1\release\address-model-64\cxxstd-03-iso\link-static\threading-multi\unique_path.obj...  
```  
  
```  
compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.1\release\address-model-64\cxxstd-03-iso\link-static\threading-multi\windows_file_codecvt.obj  
windows_file_codecvt.cpp  
libs\filesystem\src\windows_file_codecvt.cpp(43): error C2220: warning treated as error - no 'object' file generated  
libs\filesystem\src\windows_file_codecvt.cpp(43): warning C4244: 'argument': conversion from '__int64' to 'int', possible loss of data  
libs\filesystem\src\windows_file_codecvt.cpp(63): warning C4244: 'argument': conversion from '__int64' to 'int', possible loss of data  
    call "bin.v2\standalone\msvc\msvc-14.1\address-model-64\architecture-x86\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"bin.v2\libs\filesystem\build\msvc-14.1\release\address-model-64\cxxstd-03-iso\link-static\threading-multi\windows_file_codecvt.obj.rsp"   
...failed compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.1\release\address-model-64\cxxstd-03-iso\link-static\threading-multi\windows_file_codecvt.obj...  
```  
  
```  
compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.1\debug\address-model-64\cxxstd-03-iso\link-static\threading-multi\unique_path.obj  
unique_path.cpp  
libs\filesystem\src\unique_path.cpp(114): error C2220: warning treated as error - no 'object' file generated  
libs\filesystem\src\unique_path.cpp(114): warning C4267: 'argument': conversion from 'size_t' to 'DWORD', possible loss of data  
    call "bin.v2\standalone\msvc\msvc-14.1\address-model-64\architecture-x86\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"bin.v2\libs\filesystem\build\msvc-14.1\debug\address-model-64\cxxstd-03-iso\link-static\threading-multi\unique_path.obj.rsp"   
...failed compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.1\debug\address-model-64\cxxstd-03-iso\link-static\threading-multi\unique_path.obj...  
```  
  
```  
compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.1\debug\address-model-64\cxxstd-03-iso\link-static\threading-multi\windows_file_codecvt.obj  
windows_file_codecvt.cpp  
libs\filesystem\src\windows_file_codecvt.cpp(43): error C2220: warning treated as error - no 'object' file generated  
libs\filesystem\src\windows_file_codecvt.cpp(43): warning C4244: 'argument': conversion from '__int64' to 'int', possible loss of data  
libs\filesystem\src\windows_file_codecvt.cpp(63): warning C4244: 'argument': conversion from '__int64' to 'int', possible loss of data  
    call "bin.v2\standalone\msvc\msvc-14.1\address-model-64\architecture-x86\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"bin.v2\libs\filesystem\build\msvc-14.1\debug\address-model-64\cxxstd-03-iso\link-static\threading-multi\windows_file_codecvt.obj.rsp"   
...failed compile-c-c++ bin.v2\libs\filesystem\build\msvc-14.1\debug\address-model-64\cxxstd-03-iso\link-static\threading-multi\windows_file_codecvt.obj...  
```  
  
Perhaps boost::numeric_cast<...> could help resolve these.

---

## Comment 1

> Username: Lastique  
> Created at: 2018-11-24 17:55:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/79#issuecomment-441384607  

Seems to be already fixed by d889455.
