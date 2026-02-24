# #156 - mingw-w64: wants to use libpthreadGC2.a, why not libpthread.a? [Closed]

> Username: hanetzer  
> Created at: 2017-09-21 02:35:14 UTC  
> Updated at: 2018-04-18 15:48:36 UTC  
> Closed at: 2018-04-18 15:45:57 UTC  
> Url: https://github.com/boostorg/thread/issues/156  

As above. The mingw-w64 runtime can produce a set of pthread dlls and import libraries  
(usr/bin/libwinpthread-1.dll, usr/lib/libpthread.a, usr/lib/libpthread.dll.a, usr/lib/libwinpthread.a, and usr/lib/libwinpthread.dll.a) which can be used just like the linux ones,  
whereas  I have no idea where libpthreadGC2.a even comes from.

---

## Comment 1

> Username: viboes  
> Created at: 2017-09-21 05:33:07 UTC  
> Updated at: 2017-09-22 17:13:12 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331055729  

I suspect that this is not a Boost.Thread issue ;-)  
it is about pthread library.

---

## Comment 2

> Username: hanetzer  
> Created at: 2017-09-21 05:57:18 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331059107  

@viboes I don't know, but the library 'libpthreadGC2.a' is hardcoded in at least one place in the boost distro tarball, being `tools/build/src/engine/boehm_gc/threadlibs.c` around line 48 or so.

---

## Comment 3

> Username: hanetzer  
> Created at: 2017-09-21 08:22:31 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331087282  

@viboes seriously? This is a buildfile issue on your guys's end, you hardcode the check for libpthreadGC2.a in https://github.com/boostorg/thread/blob/develop/build/Jamfile.v2#L214  
This is *NOT* invalid.

---

## Comment 4

> Username: viboes  
> Created at: 2017-09-21 17:02:26 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331219399  

Sorry, you message was not clear enough.  
   
Please, create a bug issue for Boost.Build. Boost.Thread has nothing to do how Boost?Build links with external libraries :(

---

## Comment 5

> Username: hanetzer  
> Created at: 2017-09-22 00:02:38 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331313958  

@viboes well can you point me at the proper location to file said bug? boostorg/build? or on that svn/trac thing I've found boost bugs on?

---

## Comment 6

> Username: viboes  
> Created at: 2017-09-22 00:43:48 UTC  
> Updated at: 2017-09-22 00:44:05 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331319509  

I believe Boost.Build accepts issues on github  
  
https://github.com/boostorg/build/issues/

---

## Comment 7

> Username: viboes  
> Created at: 2017-09-22 17:13:52 UTC  
> Updated at: 2017-09-22 17:15:04 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331506377  

I've take a look at the Jamfile and I see something that can be related  
  
```  
         if <toolset>gcc in $(properties)  
         {  
             libname = lib$(libname)GC2.a ;  
         }  
```  
  
I'm not sure how cross-compiling should work on Boost.Thread. Any help welcome.  
  
what command are you using?

---

## Comment 8

> Username: viboes  
> Created at: 2017-09-22 17:19:25 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331507891  

Is this related https://sourceware.org/ml/pthreads-win32/2011/msg00027.html?

---

## Comment 9

> Username: viboes  
> Created at: 2017-09-22 17:33:19 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331511650  

Would https://github.com/boostorg/thread/pull/160 help?

---

## Comment 10

> Username: karzhenkov  
> Created at: 2017-09-22 20:09:14 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331548541  

@hanetzer did you try to compile Boost.Thread using `threadapi=win32` on the command line?

---

## Comment 11

> Username: hanetzer  
> Created at: 2017-09-23 01:04:21 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331594623  

@karzhenkov yes, it will compile with `threadapi=win32`, but ideally I should be able to take advantage of mingw-w64's pthread libraries.

---

## Comment 12

> Username: viboes  
> Created at: 2017-09-23 04:21:29 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331609535  

Could you tell me which command do you use and what are the values of <host-os> in your configuration?  
  
With witch library would you like to link and how would you like to tell it to Boost.Thread?

---

## Comment 13

> Username: karzhenkov  
> Created at: 2017-09-23 04:22:01 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331609558  

@viboes the problem is not about cross-compiling. With mingw-w64 on Windows (when it is both a host and a target) result is the same. The only difference is default `threadapi`, but this is the issue #160.  
  
The line containing "...GC2.a" in Jamfile exists since 2007. I guess that the mingw has changed significantly in the meantime. The "mingw-w64" and original "mingw" are related but different projects. The original "mingw" has almost no activity since 2013.

---

## Comment 14

> Username: hanetzer  
> Created at: 2017-09-23 04:43:31 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331610346  

@karzhenkov exactly. The libpthreadGC2 library is from an external project which according to some folks on irc.oftc.net/#mingw-w64 is only capable of 32-bit operation, and minw-w64's runtime provides its own pthread library, which is usually installed by default for most distro's mingw packages (which are more often than not is mingw-w64, and not the original, legacy mingw).

---

## Comment 15

> Username: viboes  
> Created at: 2017-09-23 06:08:36 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331613568  

Could one of you provide a patch for mingw-w64?

---

## Comment 16

> Username: viboes  
> Created at: 2017-09-23 06:51:56 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331615444  

Hi, I received the following  at 03:06 but it seems it was removed.  
  
@viboes  
  
compile command line:  
b2 --user-config=/tmp/portage/dev-libs/boost-1.65.0-r1/work/boost_1_65_0-abi_x86_32.x86/user-config.jam gentoorelease -j16 -q -d+2 -sICU_PATH=/usr --without-mpi --without-context --without-coroutine --without-fiber pch=off --boost-build=/usr/share/boost-build --prefix=/tmp/portage/dev-libs/boost-1.65.0-r1/image/usr --layout=system threading=multi link=shared -sNO_BZIP2=1 target-os=windows --without-python  
  
user-config.jam:  
using gcc : 6.4 : i686-w64-mingw32-g++ : <cflags>"-O2 -pipe -fomit-frame-pointer" <cxxflags>"-O2 -pipe -fomit-frame-pointer -std=c++14" <linkflags>"-Wl,-O1 -Wl,--as-needed" ;  
  
build.log:  
https://bpaste.net/show/0f4ad2771c83  
  
if you need any other info, please let me know.

---

## Comment 17

> Username: viboes  
> Created at: 2017-09-23 07:04:07 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331615960  

Could you try --without-log and report the build.log?

---

## Comment 18

> Username: hanetzer  
> Created at: 2017-09-23 10:13:39 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331624586  

@viboes actually that was a different command line, I was mistaken on it.

---

## Comment 19

> Username: viboes  
> Created at: 2017-09-24 10:42:08 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-331701573  

Ok. I propose to remove this comment and the preceding 3.

---

## Comment 20

> Username: viboes  
> Created at: 2018-02-26 15:56:25 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-368549741  

A PR fixing this issue is welcome.

---

## Comment 21

> Username: hanetzer  
> Created at: 2018-03-09 06:50:02 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-371728769  

@viboes I personally have nothing in the way of skills in this area. I would gladly fix it myself,  
but it is beyond me atm. I'm just a consumer of the library and not a direct developer using it.  
I just know that there is a more 'native' mingw-w64 pthreads library which is already there, and  
does not require an external, unmaintained package.

---

## Comment 22

> Username: Kojoley  
> Created at: 2018-03-10 22:26:18 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-372072033  

@hanetzer you can start by commenting out this line  
https://github.com/boostorg/thread/blob/e848363029b0267c8b1de98b64026f7ab044680d/build/Jamfile.v2#L270 and solving this compile errors  
  
<details>  
 <summary>Click for an output log</summary>  
  
```  
$ ../../../b2 toolset=gcc threadapi=pthread test_thread -d2  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
************************************************************  
Trying to build Boost.Thread with pthread support.  
If you need pthread you should specify the paths.  
You can specify them in site-config.jam, user-config.jam  
or in the environment.  
For example:  
PTW32_INCLUDE=C:\Program Files\ptw32\Pre-built2\include  
PTW32_LIB=C:\Program Files\ptw32\Pre-built2\lib  
************************************************************  
    - lockfree boost::atomic_flag : yes (cached)  
...patience...  
...patience...  
...found 3467 targets...  
...updating 23 targets...  
common.mkdir ..\..\..\bin.v2\libs\thread  
  
        if not exist "..\..\..\bin.v2\libs\thread\\" mkdir "..\..\..\bin.v2\libs\thread"  
  
common.mkdir ..\..\..\bin.v2\libs\thread\test  
  
        if not exist "..\..\..\bin.v2\libs\thread\test\\" mkdir "..\..\..\bin.v2\libs\thread\test"  
  
common.mkdir ..\..\..\bin.v2\libs\thread\test\test_thread.test  
  
        if not exist "..\..\..\bin.v2\libs\thread\test\test_thread.test\\" mkdir "..\..\..\bin.v2\libs\thread\test\test_thread.test"  
  
common.mkdir ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0  
  
        if not exist "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\\" mkdir "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0"  
  
common.mkdir ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug  
  
        if not exist "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug\\" mkdir "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug"  
  
common.mkdir ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread  
  
        if not exist "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\\" mkdir "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread"  
  
common.mkdir ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi  
  
        if not exist "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi\\" mkdir "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi"  
  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi\test_thread.o  
  
    "g++"   -O0 -fno-inline -Wall -pedantic -g -pthread -m64 -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_TEST_DYN_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_THROW_IF_PRECONDITION_NOT_SATISFIED -DBOOST_THREAD_USE_DLL=1 -DBOOST_TIMER_DYN_LINK=1  -I"..\..\.." -c -o "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi\test_thread.o" "test_thread.cpp"  
  
In file included from ..\..\../boost/thread/mutex.hpp:16:0,  
                 from ..\..\../boost/thread/pthread/thread_data.hpp:13,  
                 from ..\..\../boost/thread/thread_only.hpp:17,  
                 from test_thread.cpp:13:  
..\..\../boost/thread/pthread/mutex.hpp: In member function 'bool boost::timed_mutex::do_try_lock_until(const internal_platform_timepoint&)':  
..\..\../boost/thread/pthread/mutex.hpp:309:77: error: 'const internal_platform_timepoint {aka const struct boost::detail::mono_platform_timepoint}' has no member named 'getTs'; did you mean 'getNs'?  
                 int const cond_res=pthread_cond_timedwait(&cond,&m,&timeout.getTs());  
                                                                             ^~~~~  
                                                                             getNs  
In file included from ..\..\../boost/assert.hpp:58:0,  
                 from ..\..\../boost/thread/pthread/pthread_mutex_scoped_lock.hpp:10,  
                 from ..\..\../boost/thread/pthread/mutex.hpp:25,  
                 from ..\..\../boost/thread/mutex.hpp:16,  
                 from ..\..\../boost/thread/pthread/thread_data.hpp:13,  
                 from ..\..\../boost/thread/thread_only.hpp:17,  
                 from test_thread.cpp:13:  
..\..\../boost/thread/pthread/mutex.hpp:314:31: error: in argument to unary !  
                 BOOST_ASSERT(!cond_res);  
                               ^  
..\..\../boost/thread/pthread/mutex.hpp:314:17: note: in expansion of macro 'BOOST_ASSERT'  
                 BOOST_ASSERT(!cond_res);  
                 ^~~~~~~~~~~~  
In file included from ..\..\../boost/thread/thread_only.hpp:17:0,  
                 from test_thread.cpp:13:  
..\..\../boost/thread/pthread/thread_data.hpp: In member function 'void boost::thread_attributes::set_stack_size(std::size_t)':  
..\..\../boost/thread/pthread/thread_data.hpp:57:37: error: '::sysconf' has not been declared  
           std::size_t page_size = ::sysconf( _SC_PAGESIZE);  
                                     ^~~~~~~  
..\..\../boost/thread/pthread/thread_data.hpp:57:37: note: suggested alternative: 'sscanf'  
           std::size_t page_size = ::sysconf( _SC_PAGESIZE);  
                                     ^~~~~~~  
                                     sscanf  
..\..\../boost/thread/pthread/thread_data.hpp:57:46: error: '_SC_PAGESIZE' was not declared in this scope  
           std::size_t page_size = ::sysconf( _SC_PAGESIZE);  
                                              ^~~~~~~~~~~~  
..\..\../boost/thread/pthread/thread_data.hpp:57:46: note: suggested alternative: '_CVTBUFSIZE'  
           std::size_t page_size = ::sysconf( _SC_PAGESIZE);  
                                              ^~~~~~~~~~~~  
                                              _CVTBUFSIZE  
In file included from ..\..\../boost/thread/condition_variable.hpp:16:0,  
                 from ..\..\../boost/thread/thread_only.hpp:26,  
                 from test_thread.cpp:13:  
..\..\../boost/thread/pthread/condition_variable.hpp: In member function 'bool boost::condition_variable::do_wait_until(boost::unique_lock<boost::mutex>&, const internal_platform_timepoint&)':  
..\..\../boost/thread/pthread/condition_variable.hpp:128:70: error: 'const internal_platform_timepoint {aka const struct boost::detail::mono_platform_timepoint}' has no member named 'getTs'; did you mean 'getNs'?  
             cond_res=pthread_cond_timedwait(&cond,the_mutex,&timeout.getTs());  
                                                                      ^~~~~  
                                                                      getNs  
..\..\../boost/thread/pthread/condition_variable.hpp: In member function 'bool boost::condition_variable_any::do_wait_until(lock_type&, const internal_platform_timepoint&)':  
..\..\../boost/thread/pthread/condition_variable.hpp:480:73: error: 'const internal_platform_timepoint {aka const struct boost::detail::mono_platform_timepoint}' has no member named 'getTs'; did you mean 'getNs'?  
               res=pthread_cond_timedwait(&cond,&internal_mutex,&timeout.getTs());  
                                                                         ^~~~~  
                                                                         getNs  
...failed gcc.compile.c++ ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi\test_thread.o...  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi\winrt_init.o  
  
    "g++"   -O0 -fno-inline -Wall -pedantic -g -pthread -m64 -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_TEST_DYN_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_THROW_IF_PRECONDITION_NOT_SATISFIED -DBOOST_THREAD_USE_DLL=1 -DBOOST_TIMER_DYN_LINK=1  -I"..\..\.." -c -o "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi\winrt_init.o" "winrt_init.cpp"  
  
common.mkdir ..\..\..\bin.v2\libs\thread\build  
  
        if not exist "..\..\..\bin.v2\libs\thread\build\\" mkdir "..\..\..\bin.v2\libs\thread\build"  
  
common.mkdir ..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0  
  
        if not exist "..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\\" mkdir "..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0"  
  
common.mkdir ..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug  
  
        if not exist "..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\\" mkdir "..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug"  
  
common.mkdir ..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread  
  
        if not exist "..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\\" mkdir "..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread"  
  
common.mkdir ..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi  
  
        if not exist "..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi\\" mkdir "..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi"  
  
common.mkdir ..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi\pthread  
  
        if not exist "..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi\pthread\\" mkdir "..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi\pthread"  
  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi\pthread\thread.o  
  
    "g++"   -O0 -fno-inline -Wall -pedantic -g -pthread -m64 -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO -DBOOST_THREAD_POSIX -DBOOST_USE_WINDOWS_H -DWIN32_LEAN_AND_MEAN  -I"..\..\.." -c -o "..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi\pthread\thread.o" "..\..\..\libs\thread\src\pthread\thread.cpp"  
  
In file included from ..\..\../boost/thread/mutex.hpp:16:0,  
                 from ..\..\../boost/thread/pthread/thread_data.hpp:13,  
                 from ..\..\../boost/thread/thread_only.hpp:17,  
                 from ..\..\..\libs\thread\src\pthread\thread.cpp:11:  
..\..\../boost/thread/pthread/mutex.hpp: In member function 'bool boost::timed_mutex::do_try_lock_until(const internal_platform_timepoint&)':  
..\..\../boost/thread/pthread/mutex.hpp:309:77: error: 'const internal_platform_timepoint {aka const struct boost::detail::mono_platform_timepoint}' has no member named 'getTs'; did you mean 'getNs'?  
                 int const cond_res=pthread_cond_timedwait(&cond,&m,&timeout.getTs());  
                                                                             ^~~~~  
                                                                             getNs  
In file included from ..\..\../boost/assert.hpp:58:0,  
                 from ..\..\../boost/thread/pthread/pthread_mutex_scoped_lock.hpp:10,  
                 from ..\..\../boost/thread/pthread/mutex.hpp:25,  
                 from ..\..\../boost/thread/mutex.hpp:16,  
                 from ..\..\../boost/thread/pthread/thread_data.hpp:13,  
                 from ..\..\../boost/thread/thread_only.hpp:17,  
                 from ..\..\..\libs\thread\src\pthread\thread.cpp:11:  
..\..\../boost/thread/pthread/mutex.hpp:314:31: error: in argument to unary !  
                 BOOST_ASSERT(!cond_res);  
                               ^  
..\..\../boost/thread/pthread/mutex.hpp:314:17: note: in expansion of macro 'BOOST_ASSERT'  
                 BOOST_ASSERT(!cond_res);  
                 ^  
In file included from ..\..\../boost/thread/thread_only.hpp:17:0,  
                 from ..\..\..\libs\thread\src\pthread\thread.cpp:11:  
..\..\../boost/thread/pthread/thread_data.hpp: In member function 'void boost::thread_attributes::set_stack_size(std::size_t)':  
..\..\../boost/thread/pthread/thread_data.hpp:57:37: error: '::sysconf' has not been declared  
           std::size_t page_size = ::sysconf( _SC_PAGESIZE);  
                                     ^~~~~~~  
..\..\../boost/thread/pthread/thread_data.hpp:57:37: note: suggested alternative: 'sscanf'  
           std::size_t page_size = ::sysconf( _SC_PAGESIZE);  
                                     ^~~~~~~  
                                     sscanf  
..\..\../boost/thread/pthread/thread_data.hpp:57:46: error: '_SC_PAGESIZE' was not declared in this scope  
           std::size_t page_size = ::sysconf( _SC_PAGESIZE);  
                                              ^~~~~~~~~~~~  
..\..\../boost/thread/pthread/thread_data.hpp:57:46: note: suggested alternative: 'DC_PAPERSIZE'  
           std::size_t page_size = ::sysconf( _SC_PAGESIZE);  
                                              ^~~~~~~~~~~~  
                                              DC_PAPERSIZE  
In file included from ..\..\../boost/thread/condition_variable.hpp:16:0,  
                 from ..\..\../boost/thread/thread_only.hpp:26,  
                 from ..\..\..\libs\thread\src\pthread\thread.cpp:11:  
..\..\../boost/thread/pthread/condition_variable.hpp: In member function 'bool boost::condition_variable::do_wait_until(boost::unique_lock<boost::mutex>&, const internal_platform_timepoint&)':  
..\..\../boost/thread/pthread/condition_variable.hpp:128:70: error: 'const internal_platform_timepoint {aka const struct boost::detail::mono_platform_timepoint}' has no member named 'getTs'; did you mean 'getNs'?  
             cond_res=pthread_cond_timedwait(&cond,the_mutex,&timeout.getTs());  
                                                                      ^~~~~  
                                                                      getNs  
..\..\../boost/thread/pthread/condition_variable.hpp: In member function 'bool boost::condition_variable_any::do_wait_until(lock_type&, const internal_platform_timepoint&)':  
..\..\../boost/thread/pthread/condition_variable.hpp:480:73: error: 'const internal_platform_timepoint {aka const struct boost::detail::mono_platform_timepoint}' has no member named 'getTs'; did you mean 'getNs'?  
               res=pthread_cond_timedwait(&cond,&internal_mutex,&timeout.getTs());  
                                                                         ^~~~~  
                                                                         getNs  
..\..\..\libs\thread\src\pthread\thread.cpp: In function 'void boost::this_thread::yield()':  
..\..\..\libs\thread\src\pthread\thread.cpp:479:9: error: expected ';' before '}' token  
         }  
         ^  
...failed gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi\pthread\thread.o...  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi\pthread\once.o  
  
    "g++"   -O0 -fno-inline -Wall -pedantic -g -pthread -m64 -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO -DBOOST_THREAD_POSIX -DBOOST_USE_WINDOWS_H -DWIN32_LEAN_AND_MEAN  -I"..\..\.." -c -o "..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi\pthread\once.o" "..\..\..\libs\thread\src\pthread\once.cpp"  
  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi\future.o  
  
    "g++"   -O0 -fno-inline -Wall -pedantic -g -pthread -m64 -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO -DBOOST_THREAD_POSIX -DBOOST_USE_WINDOWS_H -DWIN32_LEAN_AND_MEAN  -I"..\..\.." -c -o "..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi\future.o" "..\..\..\libs\thread\src\future.cpp"  
  
...skipped <p..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi>libboost_thread_pthread-mgw73-mt-d-x64-1_67.dll.a for lack of <p..\..\..\bin.v2\libs\thread\build\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi>pthread\thread.o...  
...skipped <p..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi>test_thread.exe for lack of <p..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi>test_thread.o...  
...skipped <p..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi>test_thread.run for lack of <p..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-mingw-gnu-7.3.0\debug\threadapi-pthread\threading-multi>test_thread.exe...  
...failed updating 2 targets...  
...skipped 4 targets...  
...updated 16 targets...  
```  
  
</details>  
  
---  
  
Do not pay attention to this message:  
```  
************************************************************  
Trying to build Boost.Thread with pthread support.  
If you need pthread you should specify the paths.  
You can specify them in site-config.jam, user-config.jam  
or in the environment.  
For example:  
PTW32_INCLUDE=C:\Program Files\ptw32\Pre-built2\include  
PTW32_LIB=C:\Program Files\ptw32\Pre-built2\lib  
************************************************************  
```  
you will link by using `linkflags=-pthread` or modifying [Boost.Build src/tools/gcc.jam#L377](https://github.com/boostorg/build/blame/0dacbc3df69fbbb443f1ecaa0ee57c0157d47843/src/tools/gcc.jam#L377)

---

## Comment 23

> Username: hanetzer  
> Created at: 2018-03-11 12:23:03 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-372111304  

@Kojoley oof. that's an ugly amount of issues to fix up. I'll go ahead and give it the good  
ol' college try, may need to have some talks with the fine folks in the #mingw-w64 irc chan  
on that.

---

## Comment 24

> Username: viboes  
> Created at: 2018-04-18 15:45:31 UTC  
> Url: https://github.com/boostorg/thread/issues/156#issuecomment-382433731  

If someone has time to spend on this platform, please provide a PR.
