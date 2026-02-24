# #298 - The intro.cpp for V2 does not compile in MSVC 19.34.31937.0 [Open]

> Username: gian21391  
> Created at: 2023-02-15 16:33:11 UTC  
> Updated at: 2023-06-13 11:12:15 UTC  
> Url: https://github.com/boostorg/process/issues/298  

I tried compiling the example file intro.cpp in the example folder for v2 using the following setup:  
CMake project using vcpkg to import Boost Process 1.81. Compiled using MSVC 19.34.31937.0.  
  
The compiler output is the following:  
  
```  
>------ Build All started: Project: process_v2, Configuration: x64-debug ------  
  [1/2] Building CXX object process_v2\CMakeFiles\process_v2.dir\process_v2.cpp.obj  
  FAILED: process_v2/CMakeFiles/process_v2.dir/process_v2.cpp.obj   
  C:\PROGRA~1\MIB055~1\2022\COMMUN~1\VC\Tools\MSVC\1434~1.319\bin\Hostx64\x64\cl.exe  /nologo /TP  -external:I%LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include -external:W0 /DWIN32 /D_WINDOWS /W3 /GR /EHsc /MDd /Zi /Ob0 /Od /RTC1 -std:c++20 /showIncludes /Foprocess_v2\CMakeFiles\process_v2.dir\process_v2.cpp.obj /Fdprocess_v2\CMakeFiles\process_v2.dir\ /FS -c %LocalPath%\process_v2\process_v2\process_v2.cpp  
  Please define _WIN32_WINNT or _WIN32_WINDOWS appropriately. For example:  
  - add -D_WIN32_WINNT=0x0601 to the compiler command line; or  
  - add _WIN32_WINNT=0x0601 to your project's Preprocessor Definitions.  
  Assuming _WIN32_WINNT=0x0601 (i.e. Windows 7 target).  
%LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\process\v2\environment.hpp(504): error C2065: 'source': undeclared identifier  
%LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\process\v2\environment.hpp(505): error C2065: 'source': undeclared identifier  
%LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\process\v2\environment.hpp(723): error C2065: 'source': undeclared identifier  
%LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\process\v2\environment.hpp(724): error C2065: 'source': undeclared identifier  
%LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\process\v2\environment.hpp(969): error C2065: 'source': undeclared identifier  
%LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\process\v2\environment.hpp(970): error C2065: 'source': undeclared identifier  
%LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\process\v2\environment.hpp(1734): error C2664: 'std::vector<wchar_t,std::allocator<wchar_t>> boost::process::v2::process_environment::build_env<std::initializer_list<boost::string_view>&>(Args,void *)': cannot convert argument 2 from 'const char [1]' to 'void *'  
          with  
          [  
              Args=std::initializer_list<boost::string_view> &  
          ]  
  %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1734): note: Conversion loses qualifiers  
  %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1723): note: see declaration of 'boost::process::v2::process_environment::build_env'  
  %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1702): note: could be 'std::vector<wchar_t,std::allocator<wchar_t>> boost::process::v2::process_environment::build_env(Args &&,std::enable_if<std::is_convertible<unknown-type,boost::process::v2::wcstring_ref>::value,void>::type *)'  
  %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1734): note: Failed to specialize function template 'std::vector<wchar_t,std::allocator<wchar_t>> boost::process::v2::process_environment::build_env(Args &&,std::enable_if<std::is_convertible<unknown-type,boost::process::v2::wcstring_ref>::value,void>::type *)'  
  %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1734): note: With the following template arguments:  
  %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1734): note: 'Args=std::initializer_list<boost::string_view> &'  
  %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1734): note: while trying to match the argument list '(std::initializer_list<boost::string_view>, const char [1])'  
%LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\process\v2\environment.hpp(1735): error C2664: 'std::vector<wchar_t,std::allocator<wchar_t>> boost::process::v2::process_environment::build_env<std::initializer_list<boost::basic_string_view<wchar_t,std::char_traits<wchar_t>>>&>(Args,void *)': cannot convert argument 2 from 'const wchar_t [1]' to 'void *'  
          with  
          [  
              Args=std::initializer_list<boost::basic_string_view<wchar_t,std::char_traits<wchar_t>>> &  
          ]  
  %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1735): note: Conversion loses qualifiers  
  %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1723): note: see declaration of 'boost::process::v2::process_environment::build_env'  
  %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1702): note: could be 'std::vector<wchar_t,std::allocator<wchar_t>> boost::process::v2::process_environment::build_env(Args &&,std::enable_if<std::is_convertible<unknown-type,boost::process::v2::wcstring_ref>::value,void>::type *)'  
  %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1735): note: Failed to specialize function template 'std::vector<wchar_t,std::allocator<wchar_t>> boost::process::v2::process_environment::build_env(Args &&,std::enable_if<std::is_convertible<unknown-type,boost::process::v2::wcstring_ref>::value,void>::type *)'  
  %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1735): note: With the following template arguments:  
  %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1735): note: 'Args=std::initializer_list<boost::basic_string_view<wchar_t,std::char_traits<wchar_t>>> &'  
  %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1735): note: while trying to match the argument list '(std::initializer_list<boost::basic_string_view<wchar_t,std::char_traits<wchar_t>>>, const wchar_t [1])'  
  ninja: build stopped: subcommand failed.  
  
Build All failed.  
```

---

## Comment 1

> Username: nszhsl  
> Created at: 2023-02-20 16:48:48 UTC  
> Updated at: 2023-02-20 16:49:29 UTC  
> Url: https://github.com/boostorg/process/issues/298#issuecomment-1437304396  

@gian21391 @klemens-morgenstern SOS  
I have the same problem.

---

## Comment 2

> Username: nszhsl  
> Created at: 2023-02-23 02:57:25 UTC  
> Url: https://github.com/boostorg/process/issues/298#issuecomment-1441155378  

> I tried compiling the example file intro.cpp in the example folder for v2 using the following setup: CMake project using vcpkg to import Boost Process 1.81. Compiled using MSVC 19.34.31937.0.  
>   
> The compiler output is the following:  
>   
> ```  
> >------ Build All started: Project: process_v2, Configuration: x64-debug ------  
>   [1/2] Building CXX object process_v2\CMakeFiles\process_v2.dir\process_v2.cpp.obj  
>   FAILED: process_v2/CMakeFiles/process_v2.dir/process_v2.cpp.obj   
>   C:\PROGRA~1\MIB055~1\2022\COMMUN~1\VC\Tools\MSVC\1434~1.319\bin\Hostx64\x64\cl.exe  /nologo /TP  -external:I%LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include -external:W0 /DWIN32 /D_WINDOWS /W3 /GR /EHsc /MDd /Zi /Ob0 /Od /RTC1 -std:c++20 /showIncludes /Foprocess_v2\CMakeFiles\process_v2.dir\process_v2.cpp.obj /Fdprocess_v2\CMakeFiles\process_v2.dir\ /FS -c %LocalPath%\process_v2\process_v2\process_v2.cpp  
>   Please define _WIN32_WINNT or _WIN32_WINDOWS appropriately. For example:  
>   - add -D_WIN32_WINNT=0x0601 to the compiler command line; or  
>   - add _WIN32_WINNT=0x0601 to your project's Preprocessor Definitions.  
>   Assuming _WIN32_WINNT=0x0601 (i.e. Windows 7 target).  
> %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\process\v2\environment.hpp(504): error C2065: 'source': undeclared identifier  
> %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\process\v2\environment.hpp(505): error C2065: 'source': undeclared identifier  
> %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\process\v2\environment.hpp(723): error C2065: 'source': undeclared identifier  
> %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\process\v2\environment.hpp(724): error C2065: 'source': undeclared identifier  
> %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\process\v2\environment.hpp(969): error C2065: 'source': undeclared identifier  
> %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\process\v2\environment.hpp(970): error C2065: 'source': undeclared identifier  
> %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\process\v2\environment.hpp(1734): error C2664: 'std::vector<wchar_t,std::allocator<wchar_t>> boost::process::v2::process_environment::build_env<std::initializer_list<boost::string_view>&>(Args,void *)': cannot convert argument 2 from 'const char [1]' to 'void *'  
>           with  
>           [  
>               Args=std::initializer_list<boost::string_view> &  
>           ]  
>   %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1734): note: Conversion loses qualifiers  
>   %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1723): note: see declaration of 'boost::process::v2::process_environment::build_env'  
>   %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1702): note: could be 'std::vector<wchar_t,std::allocator<wchar_t>> boost::process::v2::process_environment::build_env(Args &&,std::enable_if<std::is_convertible<unknown-type,boost::process::v2::wcstring_ref>::value,void>::type *)'  
>   %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1734): note: Failed to specialize function template 'std::vector<wchar_t,std::allocator<wchar_t>> boost::process::v2::process_environment::build_env(Args &&,std::enable_if<std::is_convertible<unknown-type,boost::process::v2::wcstring_ref>::value,void>::type *)'  
>   %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1734): note: With the following template arguments:  
>   %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1734): note: 'Args=std::initializer_list<boost::string_view> &'  
>   %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1734): note: while trying to match the argument list '(std::initializer_list<boost::string_view>, const char [1])'  
> %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\process\v2\environment.hpp(1735): error C2664: 'std::vector<wchar_t,std::allocator<wchar_t>> boost::process::v2::process_environment::build_env<std::initializer_list<boost::basic_string_view<wchar_t,std::char_traits<wchar_t>>>&>(Args,void *)': cannot convert argument 2 from 'const wchar_t [1]' to 'void *'  
>           with  
>           [  
>               Args=std::initializer_list<boost::basic_string_view<wchar_t,std::char_traits<wchar_t>>> &  
>           ]  
>   %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1735): note: Conversion loses qualifiers  
>   %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1723): note: see declaration of 'boost::process::v2::process_environment::build_env'  
>   %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1702): note: could be 'std::vector<wchar_t,std::allocator<wchar_t>> boost::process::v2::process_environment::build_env(Args &&,std::enable_if<std::is_convertible<unknown-type,boost::process::v2::wcstring_ref>::value,void>::type *)'  
>   %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1735): note: Failed to specialize function template 'std::vector<wchar_t,std::allocator<wchar_t>> boost::process::v2::process_environment::build_env(Args &&,std::enable_if<std::is_convertible<unknown-type,boost::process::v2::wcstring_ref>::value,void>::type *)'  
>   %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1735): note: With the following template arguments:  
>   %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1735): note: 'Args=std::initializer_list<boost::basic_string_view<wchar_t,std::char_traits<wchar_t>>> &'  
>   %LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1735): note: while trying to match the argument list '(std::initializer_list<boost::basic_string_view<wchar_t,std::char_traits<wchar_t>>>, const wchar_t [1])'  
>   ninja: build stopped: subcommand failed.  
>   
> Build All failed.  
> ```  
  
Have you resolved?

---

## Comment 3

> Username: palmada  
> Created at: 2023-06-06 08:47:54 UTC  
> Url: https://github.com/boostorg/process/issues/298#issuecomment-1578209818  

Have the same issue exactly.

---

## Comment 4

> Username: Thiesius  
> Created at: 2023-06-13 11:12:15 UTC  
> Url: https://github.com/boostorg/process/issues/298#issuecomment-1589087993  

`%LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost\process\v2\environment.hpp(504): error C2065: 'source': undeclared identifier`  
This is a bug in newer MSVC.  
https://developercommunity.visualstudio.com/t/VS-22-174-templated-constructor-paramet/10202157  
It is opened for a while so I wouldn't hold my breath that this will get fixed anytime soon. Unless a workaround is implemented in boost::process.  I have managed to fix the issue with simple traits struct and concept. However the boost::process is not C++20 exclusive library, thus the workaround I made is not viable for upstream.  
  
The second issue   
`%LocalPath%\process_v2\out\build\x64-debug\vcpkg_installed\x64-windows\include\boost/process/v2/environment.hpp(1734): note: Conversion loses qualifiers`  
  
occurs because the `""` in `unicode_env{build_env(sv,  "")}` is a string literal which is implicitly const.   
The SFINAE used in `build_env` however loses the const qualifier, as the enable_if::type evaluates to `void` instead of `const void`,  
which looks to me as an issue of boost::process. It can be fixed straightforwardly by using const void instead of void in enable_if.
