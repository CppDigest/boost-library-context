# #14 - Using stacktrace with MinGW (with msys2) [Closed]

> Username: ustrobot  
> Created at: 2017-04-13 19:47:39 UTC  
> Updated at: 2017-11-24 20:25:27 UTC  
> Closed at: 2017-11-24 20:25:27 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/14  

We used the stacktrace in our cross platform development (Win,Linux, Mac). On Linux/Mac it works as expected but on Windows there are some issues:  
  
For windows we build our code using cmake+MinGW in msys2 environment as header only lib.   
  
- In default mode it does not work - always returns zero stack size. It appears that _Unwind_Backtrace always return zero stack.  
- In BOOST_STACKTRACE_USE_WINDBG mode it does not work as is. It requires to define   
```  
__CRT_UUID_DECL(IDebugClient,0x27fe5639,0x8407,0x4f47,0x83,0x64,0xee,0x11,0x8f,0xb0,0x8a,0xc8);  
__CRT_UUID_DECL(IDebugControl,0x5182e668,0x105e,0x416e,0xad,0x92,0x24,0xef,0x80,0x04,0x24,0xba);  
__CRT_UUID_DECL(IDebugSymbols,0x8c31e98c,0x983a,0x48a5,0x90,0x16,0x6f,0xe5,0xd6,0x67,0xa9,0x50);  
```  
Also it appears that symbols are not demangled. Thus we patched it by adding boost::core::demangle appending additional underscore before symbol.  
  
Look at the patch with changes that we have made to make it working for us.  
In the [patch.diff.txt](https://github.com/apolukhin/stacktrace/files/920641/patch.diff.txt) there are additional hacking to make it work with boost 1.60  
  
Many Thanks!

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-04-14 07:12:13 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/14#issuecomment-294105091  

Great thanks for the patch. I'll to merge the fixes soon.

---

## Comment 2

> Username: ArekPiekarz  
> Created at: 2017-06-21 16:30:43 UTC  
> Updated at: 2017-06-21 16:31:02 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/14#issuecomment-310134078  

It still doesn't work for me. The backtrace is always empty on Windows when compiling with gcc from msys2, even with other configuration options than BOOST_STACKTRACE_USE_WINDBG.

---

## Comment 3

> Username: apolukhin  
> Created at: 2017-06-24 08:32:27 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/14#issuecomment-310825163  

Looks like you have to use libbacktrace library by defining `BOOST_STACKTRACE_USE_BACKTRACE`. You will finde some links to the library sources here: http://boostorg.github.io/stacktrace/stacktrace/configuration_and_build.html  
  
Hopefully this will help.   
  
P.S.: I'm going to fill a feature request for MinGW to include libbacktrace by default in the MinGW distribution.

---

## Comment 4

> Username: ArekPiekarz  
> Created at: 2017-06-24 08:39:27 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/14#issuecomment-310825471  

@apolukhin Thanks for answer, I was using BOOST_STACKTRACE_USE_BACKTRACE. Maybe there is something wrong with my compiled version of libbacktrace or the way the whole test application using it is built. I will include full instructions and source code later today, so maybe you could try to reproduce it.

---

## Comment 5

> Username: ArekPiekarz  
> Created at: 2017-06-24 15:40:24 UTC  
> Updated at: 2017-06-24 18:06:47 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/14#issuecomment-310845980  

Here are prepared instructions how you can get Msys2, all dependencies and build Stacktrace example.  
At the end I included logs of building and running one example with different configurations, as well as build logs of libbacktrace and failed build with addr2line config.  
  
### Boost Stacktrace on Windows with Msys2  
  
Download and install Msys2 from http://www.msys2.org/  
(the newest 64-bit build - http://repo.msys2.org/distrib/x86_64/msys2-x86_64-20161025.exe)  
At the end of installation an MSYS console will open.  
Msys2 has 3 consoles - MSYS, MinGW 32-bit and MinGW 64-bit.  
Let's assume we want the 64-bit, so close the MSYS one and go to Windows Start menu -> MSYS2 64bit -> MSYS2 MinGW 64-bit.  
Update packages  
```  
pacboy update  
```  
When you get a warning at the end of the update to close the shell and restart it, do it.  
There will be a warning that the update process is still working, confirm closing of terminal.  
Update the rest of packages  
```  
pacboy update  
```  
Install packages for development (:x means 64-bit version)  
```  
pacboy sync msys/git msys/make boost:x gcc:x  
```  
(Optionally install dlfcn for libdl.dll, but linking to it makes no difference)  
```  
pacboy sync dlfcn:x  
```  
Get and build libbacktrace  
```  
mkdir /c/dev  
cd /c/dev  
git clone https://github.com/ianlancetaylor/libbacktrace.git  
mkdir libbacktrace-build  
cd libbacktrace-build  
../libbacktrace/configure  
make  
make install  
```  
Get Boost Stacktrace  
```  
cd /c/dev  
git clone https://github.com/boostorg/stacktrace.git  
```  
  
### Build assert_handler example with different configurations  
  
Go to Stacktrace examples  
```  
cd stacktrace/example  
```  
  
#### Basic config  
  
$ g++ -I/c/dev/stacktrace/include -o assert_handler_basic.exe assert_handler.cpp  
  
$ ./assert_handler_basic.exe  
Expression 'i < N' is false in function 'T& boost::array<T, N>::operator[](boost::array<T, N>::size_type) [with T = int; long long unsigned int N = 5ull; boost::array<T, N>::reference = int&; boost::array<T, N>::size_type = long long unsigned int]': out of range.  
Backtrace:  
  
  
#### WinDbg config  
  
$ g++ -DBOOST_STACKTRACE_USE_WINDBG -I/c/dev/stacktrace/include -o assert_handler_windbg.exe assert_handler.cpp -lole32 -lDbgEng  
In file included from C:/dev/stacktrace/include/boost/stacktrace/frame.hpp:61:0,  
                 from C:/dev/stacktrace/include/boost/stacktrace.hpp:15,  
                 from assert_handler.cpp:35:  
C:/dev/stacktrace/include/boost/stacktrace/detail/frame_msvc.ipp:167:72: warning: missing terminating ' character  
 \#   error Your compiler does not support C++11 thread_local storage. It's impossible to build with BOOST_STACKTRACE_USE_WINDBG_CACHED.  
                                                                        ^  
  
$ ./assert_handler_windbg.exe  
Expression 'i < N' is false in function 'T& boost::array<T, N>::operator[](boost::array<T, N>::size_type) [with T = int; long long unsigned int N = 5ull; boost::array<T, N>::reference = int&; boost::array<T, N>::size_type = long long unsigned int]': out of range.  
Backtrace:  
  
  
#### libbacktrace config  
  
$ g++ -DBOOST_STACKTRACE_USE_BACKTRACE -I/c/dev/stacktrace/include -o assert_handler_backtrace.exe assert_handler.cpp -lbacktrace  
  
$ ./assert_handler_backtrace.exe  
Expression 'i < N' is false in function 'T& boost::array<T, N>::operator[](boost::array<T, N>::size_type) [with T = int; long long unsigned int N = 5ull; boost::array<T, N>::reference = int&; boost::array<T, N>::size_type = long long unsigned int]': out of range.  
Backtrace:  
  
  
#### addr2line config  
  
It doesn't compile because it needs sys/wait.h, which is in package msys/msys2-runtime-devel. However trying to mix dependencies from msys and mingw64 folders leads to even more errors. Compiling with MSYS g++ instead of MinGW-64 g++ creates another set of issues. Logs are in attachments.  
  
[boost-stacktrace-addr2line-config.txt](https://github.com/boostorg/stacktrace/files/1099849/boost-stacktrace-addr2line-config.txt)  
[libbacktrace-build-log.txt](https://github.com/boostorg/stacktrace/files/1099848/libbacktrace-build-log.txt)

---

## Comment 6

> Username: apolukhin  
> Created at: 2017-06-24 17:46:00 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/14#issuecomment-310853327  

Thanks a lot! That will definitely help to solve the issue

---

## Comment 7

> Username: StephanTLavavej  
> Created at: 2017-08-06 23:36:18 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/14#issuecomment-320540169  

This bug is causing Boost 1.65.0 RC1 to fail to build for me (with MSYS2/mingw-w64), emitting the error about `thread_local`. I just sent a mail to the Boost mailing list before finding this bug, apologies for the duplicate report.

---

## Comment 8

> Username: apolukhin  
> Created at: 2017-10-13 20:09:46 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/14#issuecomment-336555169  

Done some investigations and fixes:  
* MinGW-w64 now works well with BOOST_STACKTRACE_USE_WINDBG, so if it is possible for you - use MinGW-w64  
* Created an issue on libbacktrace functionality 2 that must fix the BOOST_STACKTRACE_USE_BACKTRACE  
* Created an issue for MinGW for providing libbacktrace out of the box https://sourceforge.net/p/mingw/bugs/2356/  
* Investigating the ability to provide BOOST_STACKTRACE_USE_ADDR2LINE for Windows platforms. This will allow to use addr2line from MinGW for stacktrace decoding while the ibbacktrace functionality is not fixed.  
* After the libbacktrace functionality fix I'll add some info to the docs on how to build it. Something like  
```  
Let's assume that you've installed MinGw into C:\MinGW and downloaded libbacktrace into C:\libbacktrace-master  
* Configure & build from console:  
    C:\MinGW\msys\1.0\bin\sh.exe  
    cd /c/libbacktrace-master  
    ./configure CC=/c/MinGW/bin/g++.exe  CXX=/c/MinGW/bin/g++.exe  
    make  
* Add info to the project-config.jam in the Boost folder:  
    using gcc : 6 : "C:\\MinGW\\bin\\g++.exe" : <compileflags>-I"C:\\libbacktrace-master\\" <linkflags>-L"C:\\libbacktrace-master\\" ;   
* Now you can build stacktrace library from Boost folder:  
    b2.exe toolset=gcc-6 --with-stacktrace  
  Or just use a header only version by defining BOOST_STACKTRACE_USE_BACKTRACE fro your project  
```

---

## Comment 9

> Username: apolukhin  
> Created at: 2017-11-24 20:25:27 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/14#issuecomment-346892293  

Must work with Boost 1.66
