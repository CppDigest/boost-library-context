# #41 - Boost 1.66 - almost no function names in Msys2 [Closed]

> Username: ArekPiekarz  
> Created at: 2018-01-06 22:05:33 UTC  
> Updated at: 2018-03-05 06:19:05 UTC  
> Closed at: 2018-03-05 06:19:05 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/41  

I have used the [new docs](http://boostorg.github.io/stacktrace/stacktrace/configuration_and_build.html) to test a few configurations of Stacktrace on Msys2. It works better than in Boost 1.65, so thank you for that, but still there are almost no function names printed, on all configurations. I said "almost", because the names are displayed for Windows system libraries in WINDBG and WINDBG_CACHED configs. However, I noticed the missing names can be manually extracted afterwards from addresses using `addr2line` utility.  
  
I attached full logs of configuring and compilation of libraries in the file [logs.txt](https://github.com/boostorg/stacktrace/files/1609248/logs.txt).  
  
I hope the following testing will be useful to you.  
  
#### ENVIRONMENT  
  
I have setup and tested on two computers with a following config:  
  
Msys2 MinGW x64  
G++ 7.2.0 x64  
Boost 1.66 x64 from package mingw64/mingw-w64-x86_64-boost  
Windows 10 x64  
  
For the initial setup of Msys2 environment please see [my previous comment](https://github.com/boostorg/stacktrace/issues/14#issuecomment-310845980).  
  
#### BASIC STACKTRACE MODE  
  
```  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
$ g++ -g -o stacktrace-basic.exe main.cpp  
  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
$ ./stacktrace-basic.exe  
 0# 0x00000000004033A1 in D:\dev\project\test\stacktrace-boost166-test\stacktrace-basic.exe  
 1# 0x00000000004015A2 in D:\dev\project\test\stacktrace-boost166-test\stacktrace-basic.exe  
 2# 0x00000000004015FE in D:\dev\project\test\stacktrace-boost166-test\stacktrace-basic.exe  
 3# 0x00000000004013F7 in D:\dev\project\test\stacktrace-boost166-test\stacktrace-basic.exe  
 4# 0x000000000040152B in D:\dev\project\test\stacktrace-boost166-test\stacktrace-basic.exe  
 5# 0x00007FFF33042774 in C:\WINDOWS\System32\KERNEL32.DLL  
 6# 0x00007FFF331A0D51 in C:\WINDOWS\SYSTEM32\ntdll.dll  
  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
$ addr2line -Cfpi -e ./stacktrace-basic.exe 0x00000000004015A2  
foo() at D:\dev\project\test\stacktrace-boost166-test/main.cpp:6  
  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
$ addr2line -Cfpi -e ./stacktrace-basic.exe 0x00000000004015FE  
main at D:\dev\project\test\stacktrace-boost166-test/main.cpp:12  
  
```  
  
#### STACKTRACE WITH LIBBACKTRACE FROM https://github.com/ianlancetaylor/libbacktrace  
##### at commit 14d377e9be7c89511c472d728e1b88b4e96f1946 (May 29 11:48:14 2017 -0700)  
  
I followed [MinGW specific instructions](http://boostorg.github.io/stacktrace/stacktrace/configuration_and_build.html#stacktrace.configuration_and_build.mingw_and_mingw_w64_specific_not) except for the two last points ("using gcc : 6..." and "b2.exe...") as I understand they are only needed for non-header-only builds of Stacktrace.  
  
```  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
$ g++ -g -DBOOST_STACKTRACE_USE_BACKTRACE  -I/d/dev/project/3rdparty/libbacktrace -o stacktrace-backtrace.exe main.cpp -L/d/dev/project/3rdparty/libbacktrace/ -lbacktrace -ldl  
  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
$ ./stacktrace-backtrace.exe  
 0# 0x0000000000408C71 in D:\dev\project\test\stacktrace-boost166-test\stacktrace-backtrace.exe  
 1# 0x00000000004015A2 in D:\dev\project\test\stacktrace-boost166-test\stacktrace-backtrace.exe  
 2# 0x00000000004015FE in D:\dev\project\test\stacktrace-boost166-test\stacktrace-backtrace.exe  
 3# 0x00000000004013F7 in D:\dev\project\test\stacktrace-boost166-test\stacktrace-backtrace.exe  
 4# 0x000000000040152B in D:\dev\project\test\stacktrace-boost166-test\stacktrace-backtrace.exe  
 5# 0x00007FFF33042774 in C:\WINDOWS\System32\KERNEL32.DLL  
 6# 0x00007FFF331A0D51 in C:\WINDOWS\SYSTEM32\ntdll.dll  
  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
$ addr2line -Cfpi -e ./stacktrace-backtrace.exe 0x00000000004015A2  
foo() at D:\dev\project\test\stacktrace-boost166-test/main.cpp:6  
```  
  
  
#### STACKTRACE WITH LIBBACKTRACE FROM https://github.com/gcc-mirror/gcc/tree/master/libbacktrace  
##### at commit 156d2484120350473307c3f621726ca0fb1c4e39 (Dec 15 09:44:22 2017 +0000)  
  
Here I stumbled upon an error during libtool step:  
  
```  
Fazer@Fazer-PC MINGW64 /d/dev/project/3rdparty/gcc/libbacktrace (master)  
$ ./libtool --mode=install /usr/bin/install -c libbacktrace.la '/d/dev/project/3rdparty/gcc/libbacktrace'  
libtool: install: /usr/bin/install -c .libs/libbacktrace.lai /d/dev/project/3rdparty/gcc/libbacktrace/libbacktrace.la  
/usr/bin/install: cannot stat '.libs/libbacktrace.lai': No such file or directory  
```  
  
I assumed it only tried to put files into correct folders, so I changed the location in the command below to gcc/libbacktrace/.libs:  
  
```  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
$ g++ -g -DBOOST_STACKTRACE_USE_BACKTRACE  -I/d/dev/project/3rdparty/gcc/libbacktrace -o stacktrace-backtrace-gcc.exe main.cpp -L/d/dev/project/3rdparty/gcc/libbacktrace/.libs -lbacktrace -ldl  
  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
$ ./stacktrace-backtrace-gcc.exe  
 0# 0x0000000000408DC1 in D:\dev\project\test\stacktrace-boost166-test\stacktrace-backtrace-gcc.exe  
 1# 0x00000000004015A2 in D:\dev\project\test\stacktrace-boost166-test\stacktrace-backtrace-gcc.exe  
 2# 0x00000000004015FE in D:\dev\project\test\stacktrace-boost166-test\stacktrace-backtrace-gcc.exe  
 3# 0x00000000004013F7 in D:\dev\project\test\stacktrace-boost166-test\stacktrace-backtrace-gcc.exe  
 4# 0x000000000040152B in D:\dev\project\test\stacktrace-boost166-test\stacktrace-backtrace-gcc.exe  
 5# 0x00007FFF33042774 in C:\WINDOWS\System32\KERNEL32.DLL  
 6# 0x00007FFF331A0D51 in C:\WINDOWS\SYSTEM32\ntdll.dll  
  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
$ addr2line -Cfpi -e ./stacktrace-backtrace-gcc.exe 0x00000000004015A2  
foo() at D:\dev\project\test\stacktrace-boost166-test/main.cpp:6  
```  
  
  
#### STACKTRACE WITH WINDBG  
I am aware of [your comment](https://github.com/boostorg/stacktrace/issues/34#issuecomment-343761442) that WINDBG* configs only sometimes work, but I wanted to check them all.  
```  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
$ g++ -g -DBOOST_STACKTRACE_USE_WINDBG -o stacktrace-windbg.exe main.cpp -lole32 -ldbgeng  
  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
$ ./stacktrace-windbg.exe  
 0# 0x0000000000403511 in stacktrace_windbg  
 1# 0x000000000040167E in stacktrace_windbg  
 2# 0x00000000004016DA in stacktrace_windbg  
 3# 0x00000000004013F7 in stacktrace_windbg  
 4# 0x000000000040152B in stacktrace_windbg  
 5# _BaseThreadInitThunk in KERNEL32  
 6# _RtlUserThreadStart in ntdll  
  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
$ addr2line -Cfpi -e ./stacktrace-windbg.exe 0x000000000040167E  
foo() at D:\dev\project\test\stacktrace-boost166-test/main.cpp:6  
```  
  
  
#### STACKTRACE WITH WINDBG CACHED  
  
On first try I got an error that `thread_local` is not supported:  
  
```  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
$ g++ -g -std=c++11 -DBOOST_STACKTRACE_USE_WINDBG_CACHED -o stacktrace-windbg-cached.exe main.cpp  
In file included from D:/app/msys64/mingw64/include/boost/stacktrace/frame.hpp:59:0,  
                 from D:/app/msys64/mingw64/include/boost/stacktrace.hpp:15,  
                 from main.cpp:1:  
D:/app/msys64/mingw64/include/boost/stacktrace/detail/frame_msvc.ipp:167:5: error: #error Your compiler does not support C++11 thread_local storage. It`s impossible to build with BOOST_STACKTRACE_USE_WINDBG_CACHED.  
 #   error Your compiler does not support C++11 thread_local storage. It`s impossible to build with BOOST_STACKTRACE_USE_WINDBG_CACHED.  
     ^~~~~  
```  
  
That surprised me because it should be supported [since GCC 4.8](https://gcc.gnu.org/projects/cxx-status.html#cxx11). I found it was disabled by this:  
  
```  
// in D:\app\msys64\mingw64\include\boost\config\compiler\gcc.hpp:306  
  
#ifdef __MINGW32__  
// Currently (June 2017) thread_local is broken on mingw for all current compiler releases, see  
// https://sourceforge.net/p/mingw-w64/bugs/527/  
// Not setting this causes program termination on thread exit.  
#define BOOST_NO_CXX11_THREAD_LOCAL  
#endif  
```  
  
That bug report states it is broken only on some configurations of MinGW, not all of them. For some reason  in my x64 environment both `__MINGW32__` and `__MINGW64__` are defined (which seems like there is a bug in Boost.Config, MinGW or Msys2), so I tested in a separate project that `thread_local` indeed works and undefined it:  
  
```  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
echo -e "#undef __MINGW32__\n$(cat main.cpp)" > main.cpp  
  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
$ g++ -g -std=c++11 -DBOOST_STACKTRACE_USE_WINDBG_CACHED -o stacktrace-windbg-cached.exe main.cpp -lole32 -ldbgeng  
  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
$ ./stacktrace-windbg-cached.exe  
 0# 0x0000000000403511 in stacktrace_windbg_cached  
 1# 0x000000000040167E in stacktrace_windbg_cached  
 2# 0x00000000004016DA in stacktrace_windbg_cached  
 3# 0x00000000004013F7 in stacktrace_windbg_cached  
 4# 0x000000000040152B in stacktrace_windbg_cached  
 5# _BaseThreadInitThunk in KERNEL32  
 6# _RtlUserThreadStart in ntdll  
  
Fazer@Fazer-PC MINGW64 /d/dev/project/test/stacktrace-boost166-test  
$ addr2line -Cfpi -e ./stacktrace-windbg-cached.exe 0x000000000040167E  
foo() at D:\dev\project\test\stacktrace-boost166-test/main.cpp:7  
```  
  
#### STACKTRACE WITH ADDR2LINE  
  
I know the docs state only POSIX compilers are supported, but I tried it for the sake of completeness. As in my previous bug report comment, it requires not present file `sys/wait.h` and additionally I learned that MinGW doesn't provide `::fork` syscall, so it's impossible to use this configuration.

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-01-11 19:46:48 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/41#issuecomment-357040287  

Many thanks for checking all the configurations and filling this issue.   
  
I'll try to fix at least the `BOOST_STACKTRACE_USE_BACKTRACE` configuration and release a fixed version in Boost.1.67

---

## Comment 2

> Username: ArekPiekarz  
> Created at: 2018-01-13 09:42:03 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/41#issuecomment-357422010  

I didn't provide the content of main.cpp, so here it is:  
  
```  
#include <boost/stacktrace.hpp>  
#include <iostream>  
  
void foo()  
{  
    std::cout << boost::stacktrace::stacktrace();  
}  
  
int main()  
{  
    foo();  
    return 0;  
}  
```

---

## Comment 3

> Username: ArekPiekarz  
> Created at: 2018-03-04 14:24:26 UTC  
> Updated at: 2018-03-04 14:52:02 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/41#issuecomment-370233075  

I have some good news!  
  
I noticed that https://github.com/ianlancetaylor/libbacktrace has been updated recently after a long time of silence. I decided to give it a spin once more.  
```  
cd /d/dev/lib/libbacktrace  
git clone https://github.com/ianlancetaylor/libbacktrace.git  
cd libbacktrace  
./configure  
mingw32-make  
```  
  
Then compiled my program with libbacktrace support:  
```  
cd /d/dev/project/test/stacktrace-boost166-test2  
g++ -g -DBOOST_STACKTRACE_USE_BACKTRACE -I/d/dev/lib/libbacktrace -o stacktrace-libbacktrace.exe main.cpp -L/d/dev/lib/libbacktrace/.libs -lbacktrace  
```  
  
And run it:  
```  
$ ./stacktrace-libbacktrace.exe  
 0# boost::stacktrace::basic_stacktrace<std::allocator<boost::stacktrace::frame> >::init(unsigned long long, unsigned long long) at D:/app/msys64/mingw64/include/boost/stacktrace/stacktrace.hpp:81  
 1# foo() at D:\dev\project\test\stacktrace-boost166-test2/main.cpp:6  
 2# main at D:\dev\project\test\stacktrace-boost166-test2/main.cpp:12  
 3# __tmainCRTStartup at C:/repo/mingw-w64-crt-git/src/mingw-w64/mingw-w64-crt/crt/crtexe.c:339  
 4# mainCRTStartup at C:/repo/mingw-w64-crt-git/src/mingw-w64/mingw-w64-crt/crt/crtexe.c:217  
 5# 0x00007FFB5EDC1FE4 in C:\WINDOWS\System32\KERNEL32.DLL  
 6# 0x00007FFB60A3EFC1 in C:\WINDOWS\SYSTEM32\ntdll.dll  
```  
  
It finally prints correctly in Msys2 :-D  
Thanks for your work and time.  
  
Note: This retest was done on GCC 7.3 x64, as it was updated in Msys2 in the meantime.  
libbacktrace was at commit 177940370e4a6b2509e92a0aaa9749184e64af43 (Jan 31 15:24:01 2018 -0800).

---

## Comment 4

> Username: apolukhin  
> Created at: 2018-03-05 06:19:05 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/41#issuecomment-370320831  

Thank you for testing!  
  
P.S.: Actually I have done nothing in this ticket. Many thanks to developers who actually fixed this!
