# #79 - message box blocks the build on CI [Closed]

> Username: SSE4  
> Created at: 2019-07-29 17:02:11 UTC  
> Updated at: 2019-08-27 05:41:53 UTC  
> Closed at: 2019-08-27 05:41:53 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/79  

running b2 boost 1.70.0 build on the appveyor blocks CI with the following message box:  
```  
[Window Title]  
C:\.conan\29f42e\1\boost\bin.v2\libs\stacktrace\build\msvc-14.1\dbg\thrd-mlt\WinDbgCached_exe.pdb  
  
[Content]  
This file does not have an app associated with it for performing this action. Please install an app or, if one is already installed, create an association in the Default Apps Settings page.  
  
[OK]  
```  
it turns out that b2 launches some batch file:  
```  
cmd.exe /Q/C "C:\Users\sse4\AppData\Local\Temp\jam228-00-00.bat"  
```  
which has the following contents:  
```  
C:\.conan\29f42e\1\boost\bin.v2\libs\stacktrace\build\msvc-14.1\dbg\thrd-mlt\WinDbgCached_exe.pdb C:\.conan\29f42e\1\boost\bin.v2\libs\stacktrace\build\msvc-14.1\dbg\thrd-mlt\WinDbgCached_exe.exe > C:\.conan\29f42e\1\boost\bin.v2\libs\stacktrace\build\msvc-14.1\dbg\thrd-mlt\WinDbgCached.output  
```  
which doesn't appear to be a valid command line.  
I run b2 with the following command line:  
```  
C:\.conan\1dd93b\1\boost_1_70_0\tools\build\b2.exe  target-os=windows architecture=x86 address-model=32 binary-format=pe abi=ms --layout=system -sBOOST_BUILD_PATH=C:\.conan\1dd93b\1\boost_1_70_0\tools\build -sNO_ZLIB=0 -sNO_BZIP2=0 -sNO_LZMA=1 -sNO_ZSTD=1 runtime-link=shared threading=multi link=static variant=debug --without-python toolset=msvc  -j12 --abbreviate-paths -d2 --debug-configuration --build-dir="C:\.conan\29f42e\1"  
```  
and b2 build stuck with the following output:  
```  
Building the Boost C++ Libraries.  
  
  
    - C++11 mutex              : yes  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes  
    - Boost.Config Feature Check: cxx11_constexpr : yes  
    - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
    - Boost.Config Feature Check: cxx11_final : yes  
    - Boost.Config Feature Check: cxx11_hdr_mutex : yes  
    - Boost.Config Feature Check: cxx11_hdr_tuple : yes  
    - Boost.Config Feature Check: cxx11_lambdas : yes  
    - Boost.Config Feature Check: cxx11_noexcept : yes  
    - Boost.Config Feature Check: cxx11_nullptr : yes  
    - Boost.Config Feature Check: cxx11_rvalue_references : yes  
    - Boost.Config Feature Check: cxx11_template_aliases : yes  
    - Boost.Config Feature Check: cxx11_thread_local : yes  
    - Boost.Config Feature Check: cxx11_variadic_templates : yes  
    - has_icu builds           : no  
warning: Graph library does not contain MPI-based parallel components.  
note: to enable them, add "using mpi ;" to your user-config.jam  
    - zlib                     : yes  
    - bzip2                    : yes  
    - iconv (libc)             : no  
    - iconv (separate)         : no  
    - icu                      : no  
    - icu (lib64)              : no  
    - native-atomic-int32-supported : yes  
    - message-compiler         : yes  
    - native-syslog-supported  : no  
    - pthread-supports-robust-mutexes : no  
    - compiler-supports-ssse3  : yes  
    - compiler-supports-avx2   : yes  
    - gcc visibility           : no  
    - long double support      : yes  
warning: skipping optional Message Passing Interface (MPI) library.  
note: to enable MPI support, add "using mpi ;" to user-config.jam.  
note: to suppress this message, pass "--without-mpi" to bjam.  
note: otherwise, you can safely ignore this message.  
    - libbacktrace builds      : no  
    - addr2line builds         : no  
    - WinDbg builds            : yes  
```  
complete log: https://ci.appveyor.com/project/ConanCIintegration/conan-boost/build/job/pe3tiiw7rbvxowlj  
  
/cc @apolukhin @grafikrobot

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-08-25 14:15:57 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/79#issuecomment-524633824  

I see nothing suspicious in the Jamfile. @grafikrobot could you help me out and take a look at the https://github.com/boostorg/stacktrace/blob/293e1f43f697ecad70582724b96b868c8ebe2162/build/Jamfile.v2#L130

---

## Comment 2

> Username: SSE4  
> Created at: 2019-08-26 17:19:47 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/79#issuecomment-524947784  

this doesn't happen in 1.71.0, probably fixed somewhere in between

---

## Comment 3

> Username: apolukhin  
> Created at: 2019-08-27 05:41:53 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/79#issuecomment-525146893  

Great! Closing
