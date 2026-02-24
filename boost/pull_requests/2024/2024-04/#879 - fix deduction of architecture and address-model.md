# #879 fix deduction of architecture and address-model [Merged]

> Username: grisumbras  
> Created at: 2024-04-01 19:47:11 UTC  
> Updated at: 2024-04-22 14:15:44 UTC  
> Merged at: 2024-04-18 16:43:15 UTC  
> Closed at: 2024-04-18 16:43:15 UTC  
> Url: https://github.com/boostorg/boost/pull/879  

Fix bfgroup/b2#368

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-04-02 15:00:07 UTC  
> Url: https://github.com/boostorg/boost/pull/879#issuecomment-2032292822  

Should be against develop.

---

## Comment 2

> Username: grisumbras  
> Created_at: 2024-04-02 15:59:39 UTC  
> Url: https://github.com/boostorg/boost/pull/879#issuecomment-2032449301  

My understanding of what happened is this: originally conditional properties were evaluated only once, and so the composed features `deduced-architecture/address-model` were expanded, but ignored due to already present `architecture/address-model` in the property set. But now conditional properties are evaluated continously, so the composed feaure never goes away, and that somehow confuses the process of constructing the final property set.

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-04-18 22:45:24 UTC  
> Url: https://github.com/boostorg/boost/pull/879#issuecomment-2065445368  

@anarthal please test whether this change fixes things.

---

## Comment 4

> Username: anarthal  
> Created_at: 2024-04-20 11:04:21 UTC  
> Url: https://github.com/boostorg/boost/pull/879#issuecomment-2067639525  

This still fails - now differently: https://drone.cpp.al/boostorg/mysql/647/18/3  
Looks like something's using a deprecated check in predef.

---

## Comment 5

> Username: grisumbras  
> Created_at: 2024-04-20 11:20:07 UTC  
> Url: https://github.com/boostorg/boost/pull/879#issuecomment-2067643431  

> error: This module (predef/check/predef.jam) is OBSOLETE. Use predef/tools/check/predef.jam instead.  
  
I don't think this is a problem with address model deduction

---

## Comment 6

> Username: grisumbras  
> Created_at: 2024-04-21 08:14:10 UTC  
> Url: https://github.com/boostorg/boost/pull/879#issuecomment-2067953627  

Actually, I think predef was not the issue, and this is the problem:  
  
> OpenSSL could not be found. Don't build target fail_if_no_openssl to skip this check  
  
I suspect, you don't have 32bit OpenSSL installed.

---

## Comment 7

> Username: grisumbras  
> Created_at: 2024-04-21 08:43:08 UTC  
> Url: https://github.com/boostorg/boost/pull/879#issuecomment-2067962817  

```  
    - ssl                      : no  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
    - symlinks supported       : yes (cached)  
    - ssl                      : no [2]  
    - BOOST_COMP_GNUC >= 4.3.0 : no [3]  
    - ssl                      : no  (cached) [2]  
```  
  
There's a file in build directory (in Boost it's BOOST_ROOT/bin.v2 by default) called config.log. That file contains output of the build commands that `ac` module uses to check availability of libraries. But it only shows the output from the most recent invocation of b2. It may contain more useful information.

---

## Comment 8

> Username: anarthal  
> Created_at: 2024-04-21 13:19:19 UTC  
> Url: https://github.com/boostorg/boost/pull/879#issuecomment-2068041015  

This is not the case, as everything passes if I re enable the explicit architecture feature specification. I implemented the explicit check I'm referring to to make inherently flawed builds like this fail instead of silently succeed ignoring my targets.  
  
Does going through the reproduction steps described in https://github.com/bfgroup/b2/issues/368 work?

---

## Comment 9

> Username: anarthal  
> Created_at: 2024-04-21 13:20:13 UTC  
> Url: https://github.com/boostorg/boost/pull/879#issuecomment-2068041350  

I'm not in my PC right now, when I have the time I'll try to post the config log here.

---

## Comment 10

> Username: anarthal  
> Created_at: 2024-04-22 07:36:43 UTC  
> Url: https://github.com/boostorg/boost/pull/879#issuecomment-2068693175  

This is `config.log`:  
  
```  
root@373b7047b37f:~/boost-root# cat bin.v2/config.log   
...found 1 target...  
...found 15 targets...  
...updating 12 targets...  
clang-linux.compile.c++ bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/openssl/ssl_h.o  
In file included from bin.v2/standalone/ac/openssl/ssl_h.cpp:1:  
In file included from /usr/include/openssl/ssl.h:21:  
/usr/include/openssl/macros.h:14:10: fatal error: 'openssl/opensslconf.h' file not found  
#include <openssl/opensslconf.h>  
         ^~~~~~~~~~~~~~~~~~~~~~~  
1 error generated.  
  
    "clang++"   -std=c++20 -fvisibility-inlines-hidden -fPIC -pthread -O0 -fno-inline -Wall -Wextra -Werror -g -fvisibility=hidden       -c -o "bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/openssl/ssl_h.o" "bin.v2/standalone/ac/openssl/ssl_h.cpp"  
  
...failed clang-linux.compile.c++ bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/openssl/ssl_h.o...  
  
...updated 11 targets...  
  
...failed updating 1 target...  
   clang-linux.compile.c++ bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/openssl/ssl_h.o  
...found 5 targets...  
...updating 3 targets...  
clang-linux.compile.c++ bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/main-ssl.o  
clang-linux.link bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/ssl  
/usr/bin/ld: cannot find -lssl: No such file or directory  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "clang++"    -o "bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/ssl" -Wl,--start-group "bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/main-ssl.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -lssl -Wl,--end-group -fPIC -pthread -std=c++20 -g -fvisibility=hidden -fvisibility-inlines-hidden   
  
...failed clang-linux.link bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/ssl...  
  
...updated 2 targets...  
  
...failed updating 1 target...  
   clang-linux.link bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/ssl  
...found 5 targets...  
...updating 3 targets...  
clang-linux.compile.c++ bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/main-ssleay32.o  
clang-linux.link bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/ssleay32  
/usr/bin/ld: cannot find -lssleay32: No such file or directory  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "clang++"    -o "bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/ssleay32" -Wl,--start-group "bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/main-ssleay32.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -lssleay32 -Wl,--end-group -fPIC -pthread -std=c++20 -g -fvisibility=hidden -fvisibility-inlines-hidden   
  
...failed clang-linux.link bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/ssleay32...  
  
...updated 2 targets...  
  
...failed updating 1 target...  
   clang-linux.link bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/ssleay32  
...found 5 targets...  
...updating 5 targets...  
clang-linux.compile.c++ bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/lnk-sttc/thrd-mlt/vsblt-hdn/main-ssl.o  
clang-linux.link bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/lnk-sttc/thrd-mlt/vsblt-hdn/ssl  
/usr/bin/ld: cannot find -lssl: No such file or directory  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "clang++"    -o "bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/lnk-sttc/thrd-mlt/vsblt-hdn/ssl" -Wl,--start-group "bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/lnk-sttc/thrd-mlt/vsblt-hdn/main-ssl.o"  -Wl,-Bstatic -lssl -Wl,-Bdynamic -lrt -Wl,--end-group -pthread -std=c++20 -g -fvisibility=hidden -fvisibility-inlines-hidden   
  
...failed clang-linux.link bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/lnk-sttc/thrd-mlt/vsblt-hdn/ssl...  
  
...updated 4 targets...  
  
...failed updating 1 target...  
   clang-linux.link bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/lnk-sttc/thrd-mlt/vsblt-hdn/ssl  
...found 2 targets...  
...updating 2 targets...  
clang-linux.compile.c++ bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/lnk-sttc/thrd-mlt/vsblt-hdn/main-ssleay32.o  
clang-linux.link bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/lnk-sttc/thrd-mlt/vsblt-hdn/ssleay32  
/usr/bin/ld: cannot find -lssleay32: No such file or directory  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "clang++"    -o "bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/lnk-sttc/thrd-mlt/vsblt-hdn/ssleay32" -Wl,--start-group "bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/lnk-sttc/thrd-mlt/vsblt-hdn/main-ssleay32.o"  -Wl,-Bstatic -lssleay32 -Wl,-Bdynamic -lrt -Wl,--end-group -pthread -std=c++20 -g -fvisibility=hidden -fvisibility-inlines-hidden   
  
...failed clang-linux.link bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/lnk-sttc/thrd-mlt/vsblt-hdn/ssleay32...  
  
...updated 1 target...  
  
...failed updating 1 target...  
   clang-linux.link bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/lnk-sttc/thrd-mlt/vsblt-hdn/ssleay32  
...found 2 targets...  
...updating 2 targets...  
clang-linux.compile.c++ bin.v2/standalone/ac/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/lnk-sttc/thrd-mlt/vsblt-hdn/openssl/ssl_h.o  
  
...updated 2 targets...  
...patience...  
...found 170 targets...  
...updating 10 targets...  
clang-linux.compile.c++ bin.v2/check/predef/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/lnk-sttc/thrd-mlt/vsblt-hdn/predef_check_cc_09a06289f4560e2f553bb0cbbec71436.o  
In file included from /root/boost-root/libs/predef/tools/check/predef_check_cc_as_cpp.cpp:7:  
/root/boost-root/libs/predef/tools/check/predef_check_cc.h:11:9: error: "FAILED"  
#       error "FAILED"  
        ^  
1 error generated.  
  
    "clang++"   -std=c++20 -fvisibility-inlines-hidden -m32 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -march=i686 --target=i386-pc-linux  -DCHECK="BOOST_COMP_GNUC >= BOOST_VERSION_NUMBER(4,3,0)"   -I"/root/boost-root/libs/predef/include"  -c -o "bin.v2/check/predef/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/lnk-sttc/thrd-mlt/vsblt-hdn/predef_check_cc_09a06289f4560e2f553bb0cbbec71436.o" "/root/boost-root/libs/predef/tools/check/predef_check_cc_as_cpp.cpp"  
  
...failed clang-linux.compile.c++ bin.v2/check/predef/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/lnk-sttc/thrd-mlt/vsblt-hdn/predef_check_cc_09a06289f4560e2f553bb0cbbec71436.o...  
  
...updated 9 targets...  
  
...failed updating 1 target...  
   clang-linux.compile.c++ bin.v2/check/predef/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/lnk-sttc/thrd-mlt/vsblt-hdn/predef_check_cc_09a06289f4560e2f553bb0cbbec71436.o  
...found 185 targets...  
...updating 7 targets...  
clang-linux.compile.c++ bin.v2/libs/config/checks/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/cxx11_constexpr.o  
  
...updated 7 targets...  
...found 1 target...  
...updating 1 target...  
clang-linux.compile.c++ bin.v2/libs/config/checks/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/cxx11_decltype.o  
  
...updated 1 target...  
...found 1 target...  
...updating 1 target...  
clang-linux.compile.c++ bin.v2/libs/config/checks/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/cxx11_hdr_tuple.o  
  
...updated 1 target...  
...found 1 target...  
...updating 1 target...  
clang-linux.compile.c++ bin.v2/libs/config/checks/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/cxx11_template_aliases.o  
  
...updated 1 target...  
...found 1 target...  
...updating 1 target...  
clang-linux.compile.c++ bin.v2/libs/config/checks/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/cxx11_variadic_templates.o  
  
...updated 1 target...  
...found 10 targets...  
...updating 9 targets...  
clang-linux.compile.c++ bin.v2/libs/json/build/clng-lnx-16/dbg/adrs-mdl-32/cxstd-20-iso/thrd-mlt/vsblt-hdn/cxx11_basic_alignas.o  
  
...updated 9 targets...  
```  
  
The problem is again that `-m32` is not being passed to the config check, as described in the original issue.

---

## Comment 11

> Username: grisumbras  
> Created_at: 2024-04-22 08:23:14 UTC  
> Url: https://github.com/boostorg/boost/pull/879#issuecomment-2068788886  

This is actually very weird, because I see in the target paths that address-model=32 is being propagated to the check target. So, it should affect the build. I'm inclined to think that the problem isn't with deduction.

---

## Comment 12

> Username: pdimov  
> Created_at: 2024-04-22 08:33:15 UTC  
> Url: https://github.com/boostorg/boost/pull/879#issuecomment-2068809150  

address-model=32 is propagated (as it's explicitly specified), but maybe the architecture is not.

---

## Comment 13

> Username: pdimov  
> Created_at: 2024-04-22 08:44:17 UTC  
> Url: https://github.com/boostorg/boost/pull/879#issuecomment-2068831933  

I have a similar problem here locally: when I specify  
```  
using openssl : :  
  <include>"C:/vcpkg/installed/x64-windows/include" <search>"C:/vcpkg/installed/x64-windows/lib" <ssl-name>libssl <crypto-name>libcrypto :  
  <address-model>64 ;  
  
using openssl : :  
  <include>"C:/vcpkg/installed/x86-windows/include" <search>"C:/vcpkg/installed/x86-windows/lib" <ssl-name>libssl <crypto-name>libcrypto :  
  <address-model>32 ;  
```  
in `user-config.jam`, then try the `mysql` tests with `b2 --reconfigure libs/mysql/test`, the first configure check for `ssl` fails, then a second succeeds. No address-model is passed in config.log (because I don't pass any on the command line.) The deduced architecture and address model checks occur prior to the failed `ssl` check in config.log.  
  
This isn't fixed by this PR; as far as I can see the behavior is the same.

---

## Comment 14

> Username: pdimov  
> Created_at: 2024-04-22 08:48:17 UTC  
> Url: https://github.com/boostorg/boost/pull/879#issuecomment-2068840427  

This is the beginning of the b2 output:  
```  
C:\boost-git\develop>rd/s/q bin.v2  
  
C:\boost-git\develop>b2 libs/mysql/test  
Performing configuration checks  
  
    - default address-model    : 64-bit [1]  
    - default architecture     : x86 [1]  
    - ssl                      : no  
    - ssl                      : no  (cached)  
    - symlinks supported       : yes  
    - ssl                      : yes [2]  
    - crypto                   : yes [2]  
    - cxx11_defaulted_moves    : yes [3]  
    - cxx11_final              : yes [3]  
    - cxx11_hdr_array          : yes [3]  
    - cxx11_hdr_chrono         : yes [3]  
    - cxx11_hdr_tuple          : yes [3]  
    - cxx11_hdr_type_traits    : yes [3]  
    - cxx11_numeric_limits     : yes [3]  
    - cxx11_override           : yes [3]  
    - cxx11_smart_ptr          : yes [3]  
    - cxx11_trailing_result_types : yes [3]  
    - cxx11_template_aliases   : yes [3]  
    - cxx11_variadic_templates : yes [3]  
    - crypto                   : yes  
    - ssl                      : no  (cached)  
    - cxx11_decltype           : yes [3]  
    - GCC libquadmath and __float128 support : no [3]  
    - BOOST_COMP_GNUC >= 4.3.0 : no [3]  
  
[1] msvc-14.3  
[2] link-static  
[3] msvc-14.3/debug/link-static/python-2.7/threadapi-win32/threading-multi/visibility-hidden  
...patience...  
...patience...  
...found 8172 targets...  
...updating 188 targets...  
compile-c-c++ bin.v2\libs\context\build\msvc-14.3\debug\threading-multi\dummy.obj  
dummy.cpp  
```

---

## Comment 15

> Username: pdimov  
> Created_at: 2024-04-22 08:51:35 UTC  
> Url: https://github.com/boostorg/boost/pull/879#issuecomment-2068846870  

This is the abridged config.log output:  
```  
...found 17 targets...  
...updating 13 targets...  
msvc.write-setup-script bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat  
compile-c-c++ bin.v2\libs\config\checks\architecture\msvc-14.3\debug\threading-multi\32.obj  
32.cpp  
libs\config\checks\architecture\32.cpp(9): error C2118: negative subscript  
  
    call "bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo "libs\config\checks\architecture\32.cpp" -c -Fo"bin.v2\libs\config\checks\architecture\msvc-14.3\debug\threading-multi\32.obj"     -TP /wd4675 /EHs /GR /Zc:throwingNew /Z7 /Od /Ob0 /W3 /MDd /Zc:forScope /Zc:wchar_t /Zc:inline /favor:blend -DBOOST_ALL_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 "-I."   
  
...failed compile-c-c++ bin.v2\libs\config\checks\architecture\msvc-14.3\debug\threading-multi\32.obj...  
  
...updated 13 targets...  
  
...failed updating 1 target...  
   compile-c-c++ bin.v2\libs\config\checks\architecture\msvc-14.3\debug\threading-multi\32.obj  
...found 2 targets...  
...updating 1 target...  
compile-c-c++ bin.v2\libs\config\checks\architecture\msvc-14.3\debug\threading-multi\64.obj  
64.cpp  
  
...updated 1 target...  
...found 2 targets...  
...updating 1 target...  
compile-c-c++ bin.v2\libs\config\checks\architecture\msvc-14.3\debug\threading-multi\arm.obj  
arm.cpp  
libs\config\checks\architecture\arm.cpp(13): fatal error C1189: #error:  "Not ARM"  
  
    call "bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo "libs\config\checks\architecture\arm.cpp" -c -Fo"bin.v2\libs\config\checks\architecture\msvc-14.3\debug\threading-multi\arm.obj"     -TP /wd4675 /EHs /GR /Zc:throwingNew /Z7 /Od /Ob0 /W3 /MDd /Zc:forScope /Zc:wchar_t /Zc:inline /favor:blend -DBOOST_ALL_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 "-I."   
  
...failed compile-c-c++ bin.v2\libs\config\checks\architecture\msvc-14.3\debug\threading-multi\arm.obj...  
  
...failed updating 1 target...  
   compile-c-c++ bin.v2\libs\config\checks\architecture\msvc-14.3\debug\threading-multi\arm.obj  
```  
(more architecture checks elided)  
```  
...found 10 targets...  
...updating 8 targets...  
compile-c-c++ bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\openssl\ssl_h.obj  
ssl_h.cpp  
bin.v2\standalone\ac\openssl\ssl_h.cpp(1): fatal error C1083: Cannot open include file: 'openssl/ssl.h': No such file or directory  
  
    call "bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo "bin.v2\standalone\ac\openssl\ssl_h.cpp" -c -Fo"bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\openssl\ssl_h.obj"     -TP /wd4675 /EHs /GR /Zc:throwingNew /Z7 /Od /Ob0 /W3 /MDd /Zc:forScope /Zc:wchar_t /Zc:inline /favor:blend -DBOOST_COBALT_USE_STD_PMR=1   
  
...failed compile-c-c++ bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\openssl\ssl_h.obj...  
  
...updated 7 targets...  
  
...failed updating 1 target...  
   compile-c-c++ bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\openssl\ssl_h.obj  
...found 6 targets...  
...updating 4 targets...  
compile-c-c++ bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\main-ssl.obj  
main-ssl.cpp  
msvc.link bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\ssl.exe  
LINK : fatal error LNK1181: cannot open input file 'ssl.lib'  
  
        call "bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
 link /NOLOGO /INCREMENTAL:NO "bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\main-ssl.obj"    "ssl.lib"  /DEBUG /MACHINE:X64 /MANIFEST /subsystem:console /out:"bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\ssl.exe"    
      
...failed msvc.link bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\ssl.exe bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\ssl.pdb...  
...removing bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\ssl.pdb  
  
...updated 2 targets...  
  
...failed updating 1 target...  
   msvc.link bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\ssl.exe bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\ssl.pdb  
...found 6 targets...  
...updating 4 targets...  
compile-c-c++ bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\main-ssleay32.obj  
main-ssleay32.cpp  
msvc.link bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\ssleay32.exe  
LINK : fatal error LNK1181: cannot open input file 'ssleay32.lib'  
  
        call "bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
 link /NOLOGO /INCREMENTAL:NO "bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\main-ssleay32.obj"    "ssleay32.lib"  /DEBUG /MACHINE:X64 /MANIFEST /subsystem:console /out:"bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\ssleay32.exe"    
      
...failed msvc.link bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\ssleay32.exe bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\ssleay32.pdb...  
...removing bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\ssleay32.pdb  
  
...updated 2 targets...  
  
...failed updating 1 target...  
   msvc.link bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\ssleay32.exe bin.v2\standalone\ac\msvc-14.3\debug\threading-multi\ssleay32.pdb  
...found 5 targets...  
...updating 5 targets...  
compile-c-c++ bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\main-ssl.obj  
main-ssl.cpp  
msvc.link bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\ssl.exe  
LINK : fatal error LNK1181: cannot open input file 'ssl.lib'  
  
        call "bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
 link /NOLOGO /INCREMENTAL:NO "bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\main-ssl.obj"   "ssl.lib"   /DEBUG /MACHINE:X64 /MANIFEST /subsystem:console /out:"bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\ssl.exe"    
      
...failed msvc.link bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\ssl.exe bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\ssl.pdb...  
...removing bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\ssl.pdb  
  
...updated 3 targets...  
  
...failed updating 1 target...  
   msvc.link bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\ssl.exe bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\ssl.pdb  
...found 3 targets...  
...updating 3 targets...  
compile-c-c++ bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\main-ssleay32.obj  
main-ssleay32.cpp  
msvc.link bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\ssleay32.exe  
LINK : fatal error LNK1181: cannot open input file 'ssleay32.lib'  
  
        call "bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
 link /NOLOGO /INCREMENTAL:NO "bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\main-ssleay32.obj"   "ssleay32.lib"   /DEBUG /MACHINE:X64 /MANIFEST /subsystem:console /out:"bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\ssleay32.exe"    
      
...failed msvc.link bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\ssleay32.exe bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\ssleay32.pdb...  
...removing bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\ssleay32.pdb  
  
...updated 1 target...  
  
...failed updating 1 target...  
   msvc.link bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\ssleay32.exe bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\ssleay32.pdb  
...found 2 targets...  
...updating 2 targets...  
link.touch bin.v2\test-symlink-source  
link.mklink bin.v2\test-symlink  
symbolic link created for bin.v2\test-symlink <<===>> test-symlink-source  
  
...updated 2 targets...  
...found 6 targets...  
...updating 4 targets...  
compile-c-c++ bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\main-libssl.obj  
main-libssl.cpp  
msvc.link bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\libssl.exe  
  
...updated 4 targets...  
...found 6 targets...  
...updating 4 targets...  
compile-c-c++ bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\main-libcrypto.obj  
main-libcrypto.cpp  
msvc.link bin.v2\standalone\ac\msvc-14.3\debug\link-static\threading-multi\libcrypto.exe  
  
...updated 4 targets...  
```

---

## Comment 16

> Username: grisumbras  
> Created_at: 2024-04-22 13:06:09 UTC  
> Updated_at: 2024-04-22 13:09:54 UTC  
> Url: https://github.com/boostorg/boost/pull/879#issuecomment-2069361378  

bfgroup/b2#375   
  
The source of the problem is that module `ac` is calculating the list of relevant features (features that can affect compatibility of a target) incorrectly. It effectively only ever considers the `<link>` feature to be relevant for targets it checks. As a result, whatever the first property set is used to create a checked target, that one is used for any other property set.  
  
So, given the fact that conditional properties are evaluated one after another, whether you got correct config checks is up to pure random chance. Unless you have a multiconfig build, in which case you are sure to get incorrect config checks.  
  
Interestingly, it is only recently that conditional properties started to be evaluated repeatedly, until the property set "stabilizes". Before that they were only evaluated once, and so this random chance thing was unavoidable if you used deduced `<address-model>` or `<architecture>`.

---
