# #453 - default_launcher.h: incorrect declaration of environ for macOS [Closed]

> Username: barracuda156  
> Created at: 2025-01-17 06:45:43 UTC  
> Updated at: 2025-03-05 08:38:28 UTC  
> Closed at: 2025-03-05 08:38:28 UTC  
> Url: https://github.com/boostorg/process/issues/453  

Could we please fix this as well? https://github.com/boostorg/process/blob/a2d2753aa8fe4f58b163f76d17d1c43f144146bc/include/boost/process/v2/posix/default_launcher.hpp#L34-L36  
  
macOS should use a different declaration for `environ`. Usually the correct code looks like this:  
```  
#ifdef __APPLE__  
#include <crt_externs.h>  
#define environ (*_NSGetEnviron())  
#else  
extern char **environ;  
#endif  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-01-20 17:05:33 UTC  
> Url: https://github.com/boostorg/process/issues/453#issuecomment-2602915050  

Is this correct 100% of the time? Or should I just skip the forward decl and include the appropriate header?

---

## Comment 2

> Username: barracuda156  
> Created at: 2025-01-20 17:10:44 UTC  
> Url: https://github.com/boostorg/process/issues/453#issuecomment-2602925067  

Thank you for responding. If you mean for macOS, then   
```  
#include <crt_externs.h>  
#define environ (*_NSGetEnviron())  
```  
works for all versions, as of now at least. (While generic declaration works for some versions of macOS, but not for all.)

---

## Comment 3

> Username: barracuda156  
> Created at: 2025-01-27 10:26:02 UTC  
> Url: https://github.com/boostorg/process/issues/453#issuecomment-2615366876  

@klemens-morgenstern Turned out, it needs a fix, since linking fails otherwise:  
  
```  
:info:build darwin.compile.c++ bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/pid.o  
:info:build     "/opt/local/bin/g++-mp-14"   -fvisibility-inlines-hidden -Os -std=gnu++17 -D_GLIBCXX_USE_CXX11_ABI=0 -arch ppc  -fPIC -m32 -O3 -Wall -fvisibility=hidden -dynamic -gdwarf-2 -fexceptions -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_ATOMIC_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_CONTEXT_NO_LIB=1 -DBOOST_DATE_TIME_DYN_LINK=1 -DBOOST_DISABLE_ASSERTS -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_FILESYSTEM_NO_LIB=1 -DBOOST_PROCESS_DYN_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_NO_LIB=1 -DDATE_TIME_INLINE -DNDEBUG   -I"." -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/asio/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/assert/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/atomic/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/container_hash/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/context/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/core/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/describe/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/detail/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/filesystem/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/function_types/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/iterator/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/move/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/mp11/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/optional/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/predef/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/process/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/scope/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/smart_ptr/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/system/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/throw_exception/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/type_index/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/variant2/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/winapi/include"  -c -o "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/pid.o" "/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/process/src/pid.cpp"  
:info:build darwin.compile.c++ bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/environment.o  
:info:build     "/opt/local/bin/g++-mp-14"   -fvisibility-inlines-hidden -Os -std=gnu++17 -D_GLIBCXX_USE_CXX11_ABI=0 -arch ppc  -fPIC -m32 -O3 -Wall -fvisibility=hidden -dynamic -gdwarf-2 -fexceptions -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_ATOMIC_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_CONTEXT_NO_LIB=1 -DBOOST_DATE_TIME_DYN_LINK=1 -DBOOST_DISABLE_ASSERTS -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_FILESYSTEM_NO_LIB=1 -DBOOST_PROCESS_DYN_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_NO_LIB=1 -DDATE_TIME_INLINE -DNDEBUG   -I"." -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/asio/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/assert/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/atomic/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/container_hash/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/context/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/core/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/describe/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/detail/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/filesystem/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/function_types/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/iterator/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/move/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/mp11/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/optional/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/predef/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/process/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/scope/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/smart_ptr/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/system/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/throw_exception/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/type_index/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/variant2/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/winapi/include"  -c -o "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/environment.o" "/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/process/src/environment.cpp"  
:info:build darwin.compile.c++ bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/shell.o  
:info:build     "/opt/local/bin/g++-mp-14"   -fvisibility-inlines-hidden -Os -std=gnu++17 -D_GLIBCXX_USE_CXX11_ABI=0 -arch ppc  -fPIC -m32 -O3 -Wall -fvisibility=hidden -dynamic -gdwarf-2 -fexceptions -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_ATOMIC_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_CONTEXT_NO_LIB=1 -DBOOST_DATE_TIME_DYN_LINK=1 -DBOOST_DISABLE_ASSERTS -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_FILESYSTEM_NO_LIB=1 -DBOOST_PROCESS_DYN_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_NO_LIB=1 -DDATE_TIME_INLINE -DNDEBUG   -I"." -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/asio/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/assert/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/atomic/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/container_hash/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/context/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/core/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/describe/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/detail/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/filesystem/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/function_types/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/iterator/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/move/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/mp11/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/optional/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/predef/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/process/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/scope/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/smart_ptr/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/system/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/throw_exception/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/type_index/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/variant2/include" -I"/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/winapi/include"  -c -o "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/shell.o" "/opt/local/var/macports/build/_opt_local_ppcports_devel_boost187/boost187/work/boost_1_87_0/libs/process/src/shell.cpp"  
:info:build darwin.link.dll bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/libboost_process-mt.dylib  
:info:build     "/opt/local/bin/g++-mp-14" -dynamiclib -Wl,-single_module -install_name "/opt/local/libexec/boost/1.87/lib/libboost_process-mt.dylib"  -o "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/libboost_process-mt.dylib" "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/detail/environment_posix.o" "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/detail/environment_win.o" "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/detail/last_error.o" "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/detail/process_handle_windows.o" "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/detail/throw_error.o" "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/detail/utf8.o" "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/ext/cmd.o" "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/ext/cwd.o" "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/ext/env.o" "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/ext/exe.o" "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/ext/proc_info.o" "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/posix/close_handles.o" "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/windows/default_launcher.o" "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/environment.o" "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/error.o" "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/pid.o" "bin.v2/libs/process/build/darwin-14.2.0/release/power_32/boost.process.fs-boost/threading-multi/visibility-hidden/shell.o" "bin.v2/libs/date_time/build/darwin-14.2.0/release/power_32/threading-multi/visibility-hidden/libboost_date_time-mt.dylib" "bin.v2/libs/filesystem/build/darwin-14.2.0/release/power_32/threading-multi/visibility-hidden/libboost_filesystem-mt.dylib" "bin.v2/libs/system/build/darwin-14.2.0/release/power_32/threading-multi/visibility-hidden/libboost_system-mt.dylib" "bin.v2/libs/context/build/darwin-14.2.0/release/power_32/threading-multi/visibility-hidden/libboost_context-mt.dylib"      -headerpad_max_install_names -fPIC -m32 -fvisibility=hidden -fvisibility-inlines-hidden -Wl,-dead_strip -no_dead_strip_inits_and_terms -Wl,-headerpad_max_install_names -L/opt/local/lib -lMacportsLegacySupport -Wl,-rpath,/opt/local/lib/libgcc    
:info:build Undefined symbols:  
:info:build   "_environ", referenced from:  
:info:build       _environ$non_lazy_ptr in environment_posix.o  
:info:build      (maybe you meant: __ZN5boost7process2v219process_environment8on_setupERNS1_5posix16default_launcherERKNS_10filesystem4pathEPKPKc, _environ$non_lazy_ptr )  
:info:build ld: symbol(s) not found  
:info:build collect2: error: ld returned 1 exit status  
```

---

## Comment 4

> Username: barracuda156  
> Created at: 2025-01-27 11:17:27 UTC  
> Url: https://github.com/boostorg/process/issues/453#issuecomment-2615479950  

Empirically, what worked for me was:  
  
1. Use the appropriate declaration:  
```  
#include <crt_externs.h>  
#define environ (*_NSGetEnviron())  
```  
2. Replace `::environ` with `environ` in two places (default_laucher.hpp, environment_posix.cpp).  
  
With this, boost 1.87 finally compiles.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2025-02-27 01:34:40 UTC  
> Url: https://github.com/boostorg/process/issues/453#issuecomment-2686584279  

It used to work on Macos: https://www.gnu.org/software/gnulib/manual/html_node/environ.html

---

## Comment 6

> Username: barracuda156  
> Created at: 2025-02-27 03:07:27 UTC  
> Url: https://github.com/boostorg/process/issues/453#issuecomment-2686739420  

> It used to work on Macos: https://www.gnu.org/software/gnulib/manual/html_node/environ.html  
  
Yeah, generic declaration apparently worked on very old macOS (pre-Leopard) and then works on recent ones, but fails on several in between.

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2025-02-28 07:37:38 UTC  
> Url: https://github.com/boostorg/process/issues/453#issuecomment-2689943846  

Did you see the #465 ? Can you check it works? I would like to merge this into master before wednesday, so it's in the next release.

---

## Comment 8

> Username: barracuda156  
> Created at: 2025-02-28 11:00:03 UTC  
> Url: https://github.com/boostorg/process/issues/453#issuecomment-2690352267  

@klemens-morgenstern I am away from my hardware presently, so unfortunately cannot test the build personally (which is why I did not respond there), but given the timeline, I will update patches in my fork and request someone to test that.

---

## Comment 9

> Username: barracuda156  
> Created at: 2025-03-02 20:13:54 UTC  
> Url: https://github.com/boostorg/process/issues/453#issuecomment-2692886697  

@klemens-morgenstern On a side note, is this next release of `process` or Boost itself? I ask since there is an unfixed bug in `interprocess` library, and I cannot get any response from its upstream so far: https://github.com/boostorg/interprocess/issues/248

---

## Comment 10

> Username: barracuda156  
> Created at: 2025-03-04 20:31:48 UTC  
> Url: https://github.com/boostorg/process/issues/453#issuecomment-2698837987  

> Did you see the [#465](https://github.com/boostorg/process/pull/465) ? Can you check it works? I would like to merge this into master before wednesday, so it's in the next release.  
  
@klemens-morgenstern We got a confirmation that the build with your fixes works on 10.5 with gcc: https://forums.macrumors.com/threads/macports-development-for-powerpc-10-4-10-5-10-6-unofficial-invitation-to-cooperate.2363509/page-9?post=33776328#post-33776328
