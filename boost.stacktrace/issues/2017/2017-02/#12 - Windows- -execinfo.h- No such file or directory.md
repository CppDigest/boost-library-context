# #12 - Windows: "execinfo.h: No such file or directory" [Closed]

> Username: ilya-fiveisky  
> Created at: 2017-02-20 19:44:21 UTC  
> Updated at: 2017-04-27 19:40:20 UTC  
> Closed at: 2017-04-27 19:40:20 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12  

While building under MXE (MinGW) I get the following error:  
> In file included from ../../source/stacktrace/include/boost/stacktrace/frame.hpp:197:0,  
>                  from ../../source/stacktrace/include/boost/stacktrace.hpp:15,  
>                  from ../../source/app/main.cpp:10:  
> ../../source/stacktrace/include/boost/stacktrace/detail/frame_unwind.ipp:24:22: fatal error: execinfo.h: No such file or directory  
>   
As I can see execinfo.h is Linux specific. Can you suggest any workaround (for MinGW)?

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-02-20 20:26:01 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-281174125  

Fixed that in https://github.com/apolukhin/stacktrace/commit/6a146fe7e307c6e3789e3e04af9014c18555d274 :)  
  
Please, try it out and report the progress.

---

## Comment 2

> Username: ilya-fiveisky  
> Created at: 2017-02-20 20:49:13 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-281178185  

Well, now I get  
  
> ../../source/stacktrace/include/boost/stacktrace/detail/safe_dump_win.ipp:19:55: fatal error: boost/detail/winapi/get_current_process.hpp: No such file or directory  
  
It's because in boost version 1.60 (which is used in MXE) this header was called GetCurrentProcess.hpp. Starting from version 1.61 it redirects to get_current_process.hpp. So it can be fixed easily.  
But then you get similar problem with access_rights.hpp which was not in 1.60 ...

---

## Comment 3

> Username: apolukhin  
> Created at: 2017-02-21 07:24:58 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-281265041  

Try copying it into your Boost installation from [here](https://raw.githubusercontent.com/boostorg/winapi/develop/include/boost/detail/winapi/access_rights.hpp)

---

## Comment 4

> Username: ilya-fiveisky  
> Created at: 2017-02-21 17:22:44 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-281413580  

I was trying to build it with latest Boost version (in order to avoid previously described problems) and I got   
  
> ../../source/stacktrace/include/boost/stacktrace/detail/frame_unwind.ipp:23:36: fatal error: dlfcn.h: No such file or directory  
  
In fact it was the first error that I got during Windows cross-build but I temporally workarounded it with [this](https://github.com/dlfcn-win32/dlfcn-win32) Windows port. Unfortunately this port does not contain `dladdr` function. So even if I use it it gives me errors  
  
> ../../source/stacktrace/include/boost/stacktrace/detail/frame_unwind.ipp: In member function 'std::__cxx11::string boost::stacktrace::detail::to_string_impl_base<Base>::operator()(const void*)':  
> ../../source/stacktrace/include/boost/stacktrace/detail/frame_unwind.ipp:99:9: error: '::Dl_info' has not been declared  
>          ::Dl_info dli;  
>          ^  
> ../../source/stacktrace/include/boost/stacktrace/detail/frame_unwind.ipp:100:15: error: '::dladdr' has not been declared  
>          if (!!::dladdr(addr, &dli) && dli.dli_sname) {  
>   
  
So this port is useless and `dlfcn` looks as Linux specific.

---

## Comment 5

> Username: apolukhin  
> Created at: 2017-02-22 07:33:36 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-281591645  

As a temporary solution - comment out those lines. I'll fix the code in a more appropriate way soon.

---

## Comment 6

> Username: apolukhin  
> Created at: 2017-02-25 20:46:18 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-282511216  

Fixed in develop branch. Not all tests are passing, but the library is usable.  
  
I'll fix the remaining tests in two weeks.

---

## Comment 7

> Username: ilya-fiveisky  
> Created at: 2017-02-27 21:06:58 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-282853401  

Well, now I can build it at least. But information that it provides is not very informative and looks like this  
  
>  0# 0x000000000040F7B3  
>  1# 0x00007FDFD72EFF45  
>  2# 0x000000000040F4B9

---

## Comment 8

> Username: apolukhin  
> Created at: 2017-02-28 01:23:52 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-282912008  

Try defining the BOOST_STACKTRACE_USE_BACKTRACE macro or linking with boost_stacktrace_backtrace (some more info could be [found here](http://apolukhin.github.io/stacktrace/boost_stacktrace/configuration_and_build.html))

---

## Comment 9

> Username: ilya-fiveisky  
> Created at: 2017-02-28 16:45:29 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-283094748  

It builds and works on Linux but not on Windows where I (expectedly?) get  
  
> ../stacktrace/include/boost/stacktrace/detail/libbacktrace_impls.hpp:19:23: fatal error: backtrace.h: No such file or directory

---

## Comment 10

> Username: ilya-fiveisky  
> Created at: 2017-02-28 18:21:33 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-283121188  

One more question. Do I understand correctly that stacktrace gives function names only when used in debug version of a program and in release - only addresses?

---

## Comment 11

> Username: apolukhin  
> Created at: 2017-02-28 20:39:35 UTC  
> Updated at: 2017-02-28 20:46:26 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-283154012  

> It builds and works on Linux but not on Windows where I (expectedly?) get fatal error: backtrace.h: No such file or directory  
  
That's not good. Looks like an issue in MinGW (I'll create a feature request soon). Until then you can download and manually install [the libbacktrace library from here](https://github.com/gcc-mirror/gcc/tree/master/libbacktrace) - this will produce filenames and line numbers in stacktraces.

---

## Comment 12

> Username: apolukhin  
> Created at: 2017-02-28 20:45:53 UTC  
> Updated at: 2017-02-28 20:48:45 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-283155431  

> One more question. Do I understand correctly that stacktrace gives function names only when used in debug version of a program and in release - only addresses?  
  
On MinGW without defining `BOOST_STACKTRACE_USE_BACKTRACE` - yes. Consider compiling with -rdynamic flag, without -fisibility=hidden in release  mode to produce a better stacktraces with function names. Warning: this may affect you program startup times and size.  
  
P.S.: Looks like I need to significantly improve documentation. Sorry for not doing that long time ago.

---

## Comment 13

> Username: apolukhin  
> Created at: 2017-03-03 02:05:36 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-283843870  

> Consider compiling with -rdynamic flag, without -fisibility=hidden  
  
I've tested, this would not help you :(  
  
Are you using MinGW or mingw-w64?   
Does your MinGW has the "Dbgeng.h" header?

---

## Comment 14

> Username: ilya-fiveisky  
> Created at: 2017-03-03 02:36:03 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-283848409  

> Are you using MinGW or mingw-w64?  
> Does your MinGW has the "Dbgeng.h" header?  
  
I am using MinGW. More exactly - I am using MXE which in turn uses MinGW.  
Yes it has the "Dbgeng.h" header.

---

## Comment 15

> Username: apolukhin  
> Created at: 2017-03-03 05:38:23 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-283870432  

I've updated the **develop** branch. Could you please test it and report problems?

---

## Comment 16

> Username: ilya-fiveisky  
> Created at: 2017-03-03 19:39:28 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-284050297  

In my distribution of MinGW there is "dbgeng.h" not "Dbgeng.h". So it fails to compile because it can not find the file. If I change the file name correspondingly then I get  
  
release/main.o:main.cpp:(.text+0xa7): undefined reference to `_GUID const& __mingw_uuidof<IDebugClient>()'  
release/main.o:main.cpp:(.text+0xd1): undefined reference to `_GUID const& __mingw_uuidof<IDebugControl>()'  
release/main.o:main.cpp:(.text+0x16f): undefined reference to `__imp_CoUninitialize'  
release/main.o:main.cpp:(.text+0x202): undefined reference to `_GUID const& __mingw_uuidof<IDebugSymbols>()'  
release/main.o:main.cpp:(.text+0x79b): undefined reference to `__imp_CoUninitialize'

---

## Comment 17

> Username: apolukhin  
> Created at: 2017-03-03 21:30:46 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-284075413  

Link with the following two libraries: "ole32.lib" and "Dbgeng.lib".  
  
Great thanks for continuing helping with this issue! I's very important for me.

---

## Comment 18

> Username: ilya-fiveisky  
> Created at: 2017-03-03 21:46:08 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-284078744  

It was linked to `dbgeng` before. After I add link to `ole32` `__imp_CoUninitialize` error disappears but `__mingw_uuidof` remains:  
  
> release/main.o:main.cpp:(.text+0xa7): undefined reference to _GUID const&  _mingw_uuidof<IDebugClient>()

---

## Comment 19

> Username: apolukhin  
> Created at: 2017-03-04 00:18:42 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-284106591  

Try linking with "uuid.lib" too.  
  
Meanwhile I'll try to remove MSVC specific extensions

---

## Comment 20

> Username: ilya-fiveisky  
> Created at: 2017-03-04 00:31:43 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-284108335  

Linking to `uuid` changes nothing.

---

## Comment 21

> Username: apolukhin  
> Created at: 2017-03-04 02:20:57 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-284119476  

Try adding the following lines: https://github.com/apolukhin/stacktrace/blob/3f48887f2e24f86e75a07d8c6ac5450aa808867a/include/boost/stacktrace/detail/frame_msvc.ipp#L36-L40  
  
Has the linker issue gone? If not, then additionally uncomment the line https://github.com/apolukhin/stacktrace/blob/3f48887f2e24f86e75a07d8c6ac5450aa808867a/include/boost/stacktrace/detail/frame_msvc.ipp#L43  
  
Please, report the results back

---

## Comment 22

> Username: ilya-fiveisky  
> Created at: 2017-03-06 17:16:15 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-284465316  

For first I get:  
  
> release/main.o:main.cpp:(.text$_ZN5boost10stacktrace6detail9to_stringB5cxx11EPKNS0_5frameEy[_ZN5boost10stacktrace6detail9to_stringB5cxx11EPKNS0_5frameEy]+0x5e): undefined reference to `_GUID const& __mingw_uuidof<IDebugClient>()'  
> release/main.o:main.cpp:(.text$_ZN5boost10stacktrace6detail9to_stringB5cxx11EPKNS0_5frameEy[_ZN5boost10stacktrace6detail9to_stringB5cxx11EPKNS0_5frameEy]+0x8d): undefined reference to `_GUID const& __mingw_uuidof<IDebugControl>()'  
> release/main.o:main.cpp:(.text$_ZN5boost10stacktrace6detail9to_stringB5cxx11EPKNS0_5frameEy[_ZN5boost10stacktrace6detail9to_stringB5cxx11EPKNS0_5frameEy]+0x181): undefined reference to `_GUID const& __mingw_uuidof<IDebugSymbols>()'  
  
For second:  
  
> release/main.o:main.cpp:(.rdata$.refptr.IID_IDebugSymbols[.refptr.IID_IDebugSymbols]+0x0): undefined reference to `IID_IDebugSymbols'  
> release/main.o:main.cpp:(.rdata$.refptr.IID_IDebugControl[.refptr.IID_IDebugControl]+0x0): undefined reference to `IID_IDebugControl'  
> release/main.o:main.cpp:(.rdata$.refptr.IID_IDebugClient[.refptr.IID_IDebugClient]+0x0): undefined reference to `IID_IDebugClient'  
>

---

## Comment 23

> Username: ustrobot  
> Created at: 2017-04-13 19:59:13 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-294006318  

I has the same issues and solve them for boost 1.60 in MinGW.  [see](https://github.com/apolukhin/stacktrace/issues/14). It is only question for me why symbols are not demangled. Solution on the patch looks like strange trick.

---

## Comment 24

> Username: apolukhin  
> Created at: 2017-04-15 06:21:03 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/12#issuecomment-294275323  

Looks like the MinGW that is installed in Appveyor CI does not have "dngeng.h" at all, so I can not easily check the results.  
  
Could someone please  check out the latest master? Have the issues gone?
