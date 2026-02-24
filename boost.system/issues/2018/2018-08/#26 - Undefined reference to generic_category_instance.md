# #26 - Undefined reference to generic_category_instance [Closed]

> Username: rcdailey  
> Created at: 2018-08-16 17:18:08 UTC  
> Updated at: 2019-12-11 16:19:23 UTC  
> Closed at: 2018-08-16 18:05:43 UTC  
> Url: https://github.com/boostorg/system/issues/26  

Using Android NDK r17b, with clang and libc++ and C++14, I get:  
  
> E:/code/frontend2/source/Core/ThirdParty/boost/include\boost/system/error_code.hpp:0: error: undefined reference to 'boost::system::detail::generic_category_instance'  
> E:/code/frontend2/source/Core/ThirdParty/boost/include\boost/system/error_code.hpp:0: error: undefined reference to 'boost::system::detail::system_category_instance'  
  
This is  using Boost 1.68. I'm not able to understand why these 2 symbols are not getting defined. Is this a bug or a configuration problem?

---

## Comment 1

> Username: pdimov  
> Created at: 2018-08-16 17:22:20 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-413621567  

If you want to use C++14, you have to compile Boost.System with C++14 as well. This is the default for g++ 6 and above, but not for Clang.  
  
If the above doesn't help, I'll need more information.

---

## Comment 2

> Username: rcdailey  
> Created at: 2018-08-16 17:24:39 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-413622284  

Do I just add `<cxxflags>-std=c++14` in the `user-config.jam` file?

---

## Comment 3

> Username: pdimov  
> Created at: 2018-08-16 17:31:10 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-413624233  

You could, but it's probably better to use the dedicated `cxxstd` feature. You could add it to `user-config` as `<cxxstd>14`, or pass it when building, as in `b2 cxxstd=14`.

---

## Comment 4

> Username: rcdailey  
> Created at: 2018-08-16 17:43:14 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-413627799  

Confirmed that specifying `cxxstd=14` and rebuilding boost static libraries has resolved this issue. For future reference, does boost need to be strictly rebuilt any time I want to upgrade the coding standard? For example, if I want my own code to use C++17, but I built boost with C++14 (but still with the same compiler and STL between both), do I need to rebuild boost again? Or is this an edge case?

---

## Comment 5

> Username: pdimov  
> Created at: 2018-08-16 17:57:18 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-413631998  

This is an edge case at the moment, but I wouldn't be surprised if in the future more Boost code starts being sensitive to the C++ standard it's compiled with. Maintaining binary compatibility across C++ standards isn't easy and we'll probably need to tackle this problem somehow at some point, not sure how.  
  
Now for Boost.System specifically, if you build with C++14 you should be able to use it from 03/11/14/17/2a, and 03<->11 works in both directions, but I couldn't make 14 work against 03/11. :-)

---

## Comment 6

> Username: rcdailey  
> Created at: 2018-08-16 18:05:43 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-413634550  

Thanks so much for your help. I think cross compiling boost to Android is difficult enough to make this issue negligible :-). I'll go ahead and close this issue now.

---

## Comment 7

> Username: agentS  
> Created at: 2018-09-15 11:26:09 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-421554153  

I'm having the same problem when linking the `boost_system` library in my C++14 project. I followed the advice provided in this issue and built boost using the commands `./b2 cxxstd=14` using g++ 7.3.0. I only built a selection of static libraries I need, as you can see in my bootstrapping command: `./bootstrap.sh --prefix=/usr/local/boost/1_68_0 --with-libraries=chrono,system,thread,timer,signals,random,date_time,coroutine`  
  
Below you can see my compile and linking commands with their relevant output:  
  
    g++ -g -std=c++14 -Wpedantic -Wall -Wconversion -Werror=vla -Wextra -O0 -c main.cpp  
    g++ -Wpedantic -Wall -o computerMonitorTransmitter main.o -lboost_thread -lpthread -lboost_system  
    main.o: In Funktion »boost::system::system_category()«:  
    /usr/local/boost/1_68_0/include/boost/system/error_code.hpp:473: Warnung: undefinierter Verweis auf »boost::system::detail::system_category_instance«  
    main.o: In Funktion »boost::system::generic_category()«:  
    /usr/local/boost/1_68_0/include/boost/system/error_code.hpp:478: Warnung: undefinierter Verweis auf »boost::system::detail::generic_category_instance«  
    collect2: error: ld returned 1 exit status

---

## Comment 8

> Username: agentS  
> Created at: 2018-09-15 11:27:04 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-421554201  

I'm sorry, but being pretty new to boost, I couldn't really find out a solution for that issue.

---

## Comment 9

> Username: pdimov  
> Created at: 2018-09-15 15:57:55 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-421591020  

In `g++ -Wpedantic -Wall -o computerMonitorTransmitter main.o -lboost_thread -lpthread -lboost_system` you're probably linking to the preinstalled Boost libraries in `/usr/lib`, rather than the ones you've built in `/usr/local/boost/1_68_0/stage/lib`.

---

## Comment 10

> Username: Kojoley  
> Created at: 2018-09-15 17:10:07 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-421600980  

I am now getting linkage errors at building Chrono and Filesystem on MSVC 14.1 ([Spirit Appveyor build](https://ci.appveyor.com/project/Kojoley/spirit-jod4k/build/1.0.206)). Cannot say when it started, but not more than a month ago.  
```  
   Creating library bin.v2\libs\filesystem\build\msvc-14.1\release\address-model-64\threading-multi\boost_filesystem-vc141-mt-x64-1_69.lib and object bin.v2\libs\filesystem\build\msvc-14.1\release\address-model-64\threading-multi\boost_filesystem-vc141-mt-x64-1_69.exp  
codecvt_error_category.obj : error LNK2019: unresolved external symbol "class boost::system::detail::generic_error_category boost::system::detail::generic_category_instance" (?generic_category_instance@detail@system@boost@@3Vgeneric_error_category@123@A) referenced in function "public: virtual bool __cdecl boost::system::error_category::std_category::equivalent(class std::error_code const &,int)const " (?equivalent@std_category@error_category@system@boost@@UEBA_NAEBVerror_code@std@@H@Z)  
operations.obj : error LNK2001: unresolved external symbol "class boost::system::detail::generic_error_category boost::system::detail::generic_category_instance" (?generic_category_instance@detail@system@boost@@3Vgeneric_error_category@123@A)  
operations.obj : error LNK2019: unresolved external symbol "class boost::system::detail::system_error_category boost::system::detail::system_category_instance" (?system_category_instance@detail@system@boost@@3Vsystem_error_category@123@A) referenced in function "public: __cdecl boost::system::error_code::error_code(void)" (??0error_code@system@boost@@QEAA@XZ)  
unique_path.obj : error LNK2001: unresolved external symbol "class boost::system::detail::system_error_category boost::system::detail::system_category_instance" (?system_category_instance@detail@system@boost@@3Vsystem_error_category@123@A)  
bin.v2\libs\filesystem\build\msvc-14.1\release\address-model-64\threading-multi\boost_filesystem-vc141-mt-x64-1_69.dll : fatal error LNK1120: 2 unresolved externals  
        call "bin.v2\standalone\msvc\msvc-14.1\address-model-64\architecture-x86\msvc-setup.bat"  >nul  
 link /NOLOGO /INCREMENTAL:NO /DLL /MACHINE:X64 /MANIFEST /subsystem:console /out:"bin.v2\libs\filesystem\build\msvc-14.1\release\address-model-64\threading-multi\boost_filesystem-vc141-mt-x64-1_69.dll" /IMPLIB:"bin.v2\libs\filesystem\build\msvc-14.1\release\address-model-64\threading-multi\boost_filesystem-vc141-mt-x64-1_69.lib"    @"bin.v2\libs\filesystem\build\msvc-14.1\release\address-model-64\threading-multi\boost_filesystem-vc141-mt-x64-1_69.dll.rsp"  
        if %ERRORLEVEL% NEQ 0 EXIT %ERRORLEVEL%  
      
...failed msvc.link.dll bin.v2\libs\filesystem\build\msvc-14.1\release\address-model-64\threading-multi\boost_filesystem-vc141-mt-x64-1_69.dll bin.v2\libs\filesystem\build\msvc-14.1\release\address-model-64\threading-multi\boost_filesystem-vc141-mt-x64-1_69.lib...  
```  
  
```  
   Creating library bin.v2\libs\chrono\build\msvc-14.1\release\address-model-64\threading-multi\boost_chrono-vc141-mt-x64-1_69.lib and object bin.v2\libs\chrono\build\msvc-14.1\release\address-model-64\threading-multi\boost_chrono-vc141-mt-x64-1_69.exp  
chrono.obj : error LNK2019: unresolved external symbol "class boost::system::detail::system_error_category boost::system::detail::system_category_instance" (?system_category_instance@detail@system@boost@@3Vsystem_error_category@123@A) referenced in function "public: void __cdecl boost::system::error_code::clear(void)" (?clear@error_code@system@boost@@QEAAXXZ)  
thread_clock.obj : error LNK2001: unresolved external symbol "class boost::system::detail::system_error_category boost::system::detail::system_category_instance" (?system_category_instance@detail@system@boost@@3Vsystem_error_category@123@A)  
process_cpu_clocks.obj : error LNK2001: unresolved external symbol "class boost::system::detail::system_error_category boost::system::detail::system_category_instance" (?system_category_instance@detail@system@boost@@3Vsystem_error_category@123@A)  
bin.v2\libs\chrono\build\msvc-14.1\release\address-model-64\threading-multi\boost_chrono-vc141-mt-x64-1_69.dll : fatal error LNK1120: 1 unresolved externals  
        call "bin.v2\standalone\msvc\msvc-14.1\address-model-64\architecture-x86\msvc-setup.bat"  >nul  
 link /NOLOGO /INCREMENTAL:NO /DLL /MACHINE:X64 /MANIFEST /subsystem:console /out:"bin.v2\libs\chrono\build\msvc-14.1\release\address-model-64\threading-multi\boost_chrono-vc141-mt-x64-1_69.dll" /IMPLIB:"bin.v2\libs\chrono\build\msvc-14.1\release\address-model-64\threading-multi\boost_chrono-vc141-mt-x64-1_69.lib"    @"bin.v2\libs\chrono\build\msvc-14.1\release\address-model-64\threading-multi\boost_chrono-vc141-mt-x64-1_69.dll.rsp"  
        if %ERRORLEVEL% NEQ 0 EXIT %ERRORLEVEL%  
      
...failed msvc.link.dll bin.v2\libs\chrono\build\msvc-14.1\release\address-model-64\threading-multi\boost_chrono-vc141-mt-x64-1_69.dll bin.v2\libs\chrono\build\msvc-14.1\release\address-model-64\threading-multi\boost_chrono-vc141-mt-x64-1_69.lib...  
```

---

## Comment 11

> Username: Kojoley  
> Created at: 2018-09-15 21:22:20 UTC  
> Updated at: 2018-09-15 21:32:16 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-421636961  

It was painfully slow, but I did bisect and found the first bad commit https://github.com/boostorg/boost/commit/0801ddc6f9a8d5c761c6dc5044804657880dbb28 which is https://github.com/boostorg/config/commit/d229f9be9670f6721e619583609d1e43207cf1ae.

---

## Comment 12

> Username: Kojoley  
> Created at: 2018-09-15 22:10:19 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-421643370  

Neither `cxxstd=14` nor `cxxflags=/std:c++14` helps, but with `define=BOOST_NO_CXX14_CONSTEXPR` it is ok.  
  
Variant | Result  
--- | ---  
b2 toolset=msvc-14.1 link=static | Fine  
b2 toolset=msvc-14.1 link=shared | Link error  
b2 toolset=msvc-14.1 link=shared cxxstd=14 | Link error  
b2 toolset=msvc-14.1 link=shared define=BOOST_NO_CXX14_CONSTEXPR | Fine

---

## Comment 13

> Username: jzmaddock  
> Created at: 2018-09-16 07:33:14 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-421716294  

That's weird, I've updated system/chrono/filesystem to latest develop and all are building fine for me checking all variants with:  
  
```  
cd libs/chrono/build  
../../../b2  msvc-14.1 -j4 release debug address-model=32,64 cxxstd=14,17 link=shared  
```  
  
I have  
  
```  
_MSC_FULL_VER                           =191526726  
```  
  
what is yours at?

---

## Comment 14

> Username: pdimov  
> Created at: 2018-09-16 11:03:46 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-421745959  

I fixed the link errors with https://github.com/boostorg/system/commit/4b7018de8581734bec21f972a9bb3d6ff11d723e, took care of an issue with one of the tests with https://github.com/boostorg/system/commit/02ea086173149473a35735b84323737e39197a96, but at the end had to disable `constexpr` again with https://github.com/boostorg/system/commit/3bdea5dfa3b85bde0aad2c7c7d4c31e18eaf9ed8 because the categories suffered from static initialization order issues.

---

## Comment 15

> Username: Kojoley  
> Created at: 2018-09-16 11:10:11 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-421746828  

@pdimov, thanks for fixing it!

---

## Comment 16

> Username: ohhmm  
> Created at: 2018-11-26 12:08:56 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-441617203  

-DBOOST_SYSTEM_NO_DEPRECATED  
    -DBOOST_ERROR_CODE_HEADER_ONLY

---

## Comment 17

> Username: EkremH  
> Created at: 2019-12-11 14:00:58 UTC  
> Updated at: 2019-12-11 14:21:34 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-564554841  

I'm having trouble in my project hitting this same linker error.  
Our code is cross platform. On Windows, all builds fine (using MSVC2017 without enforcing any specific C++ standard level). On Linux using clang 7.0 with C++17 which throws this linker error. We use boost 1.68.0 through public bincrafters boost package (on conan). i.e. we don't manually build boost.  
  
I understand recommendation is to build using  `b2 cxxstd=14`. However looking at the conan recipe on bincrafters it doesn't look like we can set this property using conan directly for boost. Or at least I don't see how.  
  
I have tried using  conan setting `compiler.cppstd=17` that forces all dependencies to re-build using C++17. That triggered rebuilds (I verified to the extend that a boost_filesystem package was created with conan config showing C++17, however could not directly verify that actual obj files were built using this setting). In the end, this did not get rid of this linker error on my project.  
  
I see two other suggestions on related threads:  
1. To use `-DBOOST_ERROR_CODE_HEADER_ONLY`  
2. To upgrade to 1.69.0 where filesystem is implemented header only.  
  
Any tips on how should I proceed? Should I attempt above alternative fixes or should i keep digging into getting boost conan package to build using C++17 correctly? Any tips appreciated.

---

## Comment 18

> Username: pdimov  
> Created at: 2019-12-11 14:45:51 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-564577529  

If I read the linked https://github.com/bincrafters/conan-boost_base/blob/testing/2.0.0/conanfile.py#L903 correctly, it seems to me that on clang with libc++ the bincrafters package unconditionally applies `<cxxflags>-std=c++11`, which may override the `conan` setting. But I'm not familiar with either `conan` or the bincrafters packages, so I can't really say. Maybe @grafikrobot knows.  
  
Upgrading to 1.69 should fix it. Or you might try creating an issue in bincrafters/conan-boost_base.

---

## Comment 19

> Username: EkremH  
> Created at: 2019-12-11 14:53:37 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-564581302  

@pdimov Thanks for the insight. I removed that link from my comment because I got the wrong link pointing to the head. We actually use v1.68 which is: https://github.com/bincrafters/conan-boost_base/blob/stable/1.68.0/conanfile.py. This version doesn't set cxxflags at all. I'll open an issue with bincrafters.

---

## Comment 20

> Username: pdimov  
> Created at: 2019-12-11 16:19:22 UTC  
> Url: https://github.com/boostorg/system/issues/26#issuecomment-564619968  

One other potentially interesting thing here is that C++14 is actually default for Clang 7. So if System is being compiled with C++11, something is overriding the default.
