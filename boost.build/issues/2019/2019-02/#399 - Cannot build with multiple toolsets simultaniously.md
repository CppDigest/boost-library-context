# #399 - Cannot build with multiple toolsets simultaniously [Closed]

> Username: Kojoley  
> Created at: 2019-02-27 00:01:26 UTC  
> Updated at: 2021-03-15 00:40:12 UTC  
> Closed at: 2019-04-01 18:07:26 UTC  
> Url: https://github.com/boostorg/build/issues/399  

The problem affects GCC and Clang, but not MSVC.  
  
`b2 toolset=gcc,gcc-6`  
  
```  
error: Name clash for '<p/home/nick/boost-root/stage/lib>libboost_atomic.so.1.70.0'  
error:   
error: Tried to build the target twice, with property sets having   
error: these incompatible properties:  
error:   
error:     -  <toolset-gcc:version>6.3.0  
error:     -  <toolset-gcc:version>6  
error:   
error: Please make sure to have consistent requirements for these   
error: properties everywhere in your project, especially for install  
error: targets.  
```  
  
`b2 toolset=clang-3.8,clang-7`  
  
```  
error: Name clash for '<p/home/nick/boost-root/stage/lib>libboost_atomic.so.1.70.0'  
error:   
error: Tried to build the target twice, with property sets having   
error: these incompatible properties:  
error:   
error:     -  <toolset-clang:version>3.8  
error:     -  <toolset-clang:version>7  
error:   
error: Please make sure to have consistent requirements for these   
error: properties everywhere in your project, especially for install  
error: targets.  
```  
  
`b2 toolset=clang,clang-7`  
  
```  
error: Name clash for '<p/home/nick/boost-root/stage/lib>libboost_atomic.so.1.70.0'  
error:   
error: Tried to build the target twice, with property sets having   
error: these incompatible properties:  
error:   
error:     -  <toolset-clang:version>3.8.1  
error:     -  <toolset-clang:version>7  
error:   
error: Please make sure to have consistent requirements for these   
error: properties everywhere in your project, especially for install  
error: targets.  
```  
  
The `b2 toolset=msvc,msvc-14`, `b2 toolset=msvc,msvc-14.1`, `b2 toolset=msvc-14.0,msvc-14.1` are fine.

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-02-27 00:32:05 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-467673041  

The same thing is with multiple `stdcxx`. I understand that staging with multiple toolsets/stdcxx is not possible, but the problem is that I cannot test the libraries too if the tests have dependencies on buildable libraries.

---

## Comment 2

> Username: grafikrobot  
> Created at: 2019-02-27 01:31:21 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-467686498  

Can you post your user-config.jam and project-config.jam (if any). And the output when using the above options with `--debug-configuration`.

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-02-27 01:38:58 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-467688096  

`./b2 toolset=clang-3.8,clang-7 --debug-configuration`  
  
no `user-config.jam`  
[project-config.jam.txt](https://github.com/boostorg/build/files/2908189/project-config.jam.txt)  
[log.txt](https://github.com/boostorg/build/files/2908188/log.txt)

---

## Comment 4

> Username: grafikrobot  
> Created at: 2019-02-27 02:54:51 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-467703522  

Looking at your project-config and log I see the problem. You do not have multiple toolsets configured. For msvc this works as it defaults to discovering and configuring all available msvc installed toolsets. For other toolsets you need to specify them if it's more than one and not trivially discovered (i.e. it's g++ or clang++ in the path). The usual place to do that is in user-config.jam.

---

## Comment 5

> Username: Kojoley  
> Created at: 2019-02-27 12:40:18 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-467847134  

> You do not have multiple toolsets configured.  
  
```  
nick@nick-debian:~/boost-root$ clang++-7 --version  
clang version 7.0.1-svn348686-1~exp1~20190111074544.62 (branches/release_70)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
nick@nick-debian:~/boost-root$ clang++-6.0 --version  
clang version 6.0.1-svn334776-1~exp1~20190123223524.117 (branches/release_60)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
nick@nick-debian:~/boost-root$ clang++-5.0 --version  
clang version 5.0.2-svn328729-1~exp1~20180509123438.100 (branches/release_50)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
```  
  
`user-config.jam`  
```  
import toolset ;  
  
using clang : 5.0 : clang++-5.0 ;  
using clang : 6.0 : clang++-6.0 ;  
using clang : 7 : clang++-7 ;  
```  
  
same `project-config.jam.txt`  
  
`./b2 toolset=clang-5.0,clang-6.0,clang-7 --debug-configuration`  
[log.txt](https://github.com/boostorg/build/files/2910287/log.txt)  
  
`./b2 toolset=clang-6.0,clang-7 --debug-configuration`  
[log.txt](https://github.com/boostorg/build/files/2910294/log.txt)

---

## Comment 6

> Username: Kojoley  
> Created at: 2019-02-27 14:08:34 UTC  
> Updated at: 2019-02-27 14:35:17 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-467874728  

The more surprising thing: Spirit examples have a name clash https://github.com/boostorg/spirit/blob/c36e74be1bd0b00b1b01f0bd5f33494f0274558c/example/qi/compiler_tutorial/Jamfile#L10-L11 but it is not detected when only Clangs or GCCs used (`./b2 toolset=clang-6.0,clang-7 libs/spirit/example` or `./b2 toolset=gcc,gcc-6 libs/spirit/example`), but is detected when trying simultaneously Clang and GCC (`./b2 toolset=gcc-6,clang-7.0 libs/spirit/example`)  
  
```  
error: Name clash for '<plibs/spirit/example/qi/compiler_tutorial/bin/debug>calc1'  
error:   
error: Tried to build the target twice, with property sets having   
error: these incompatible properties:  
error:   
error:     -  <toolset-gcc:version>6 <toolset>gcc  
error:     -  <toolset-clang:platform>linux <toolset-clang:version>7 <toolset>clang  
error:   
error: Please make sure to have consistent requirements for these   
error: properties everywhere in your project, especially for install  
error: targets.  
```  
  
FYI: I removed the name clash on develop https://github.com/boostorg/spirit/commit/5c1430406834967b1774e83d9ff11d0d1a44e861

---

## Comment 7

> Username: pdimov  
> Created at: 2019-03-12 03:46:36 UTC  
> Updated at: 2019-03-12 03:47:04 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-471845390  

This is a likely a manifestation of the same problem as in #404 and #410 - the toolsets assume that the relevant portions of the version are _major_._minor_, but since gcc 5 and clang 7 it's just _major_. (Actually for clang it's more convoluted because for clang 4-6 only the major version is relevant but the command is still clang++-4.0, whereas for 7 the command has been changed to clang++-7.)

---

## Comment 8

> Username: swatanabe  
> Created at: 2019-03-12 18:15:12 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-472120938  

AMDG  
  
This isn't a problem with Boost.Build.  It's a problem with  
a specific project.  You haven't indicated which project  
you're trying to build (although from the logs, it looks  
like you're running b2 at the top level).  
  
> I understand that staging with multiple toolsets/stdcxx is not possible  
  
It looks like that's exactly what you're trying to do.  
  
> but the problem is that I cannot test the libraries too if the tests have dependencies on buildable libraries.  
  
I can run  
$ cd status  
$ b2 clang gcc  
  
without any problem.  
  
In Christ,  
Steven Watanabe

---

## Comment 9

> Username: Kojoley  
> Created at: 2019-03-12 18:26:21 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-472125524  

> This isn't a problem with Boost.Build.  It's a problem with  
> a specific project.  You haven't indicated which project  
> you're trying to build (although from the logs, it looks  
> like you're running b2 at the top level).  
  
Yes, I am building top level. I need to build all non-header-only libraries before testing, because I run tests with `warnings-as-errors=on` (library maintainers do not bother fixing warnings in non headers).  
  
> I can run  
> $ cd status  
> $ b2 clang gcc  
>   
> without any problem.  
  
`b2 clang gcc` you need to have multiple GCCs here or multiple Clangs to trigger the issue, alternatively you can use cxxstd `b2 toolset=clang cxxstd=03,11`  
  
<details>  
  
```  
C:\Working\Repositories\boost>b2 toolset=clang cxxstd=03,11  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes (cached)  
    - C++11 mutex              : no  
    - Boost.Config Feature Check: cxx11_auto_declarations : no  
    - Boost.Config Feature Check: cxx11_constexpr : no  
    - Boost.Config Feature Check: cxx11_defaulted_functions : no  
    - Boost.Config Feature Check: cxx11_final : no  
    - Boost.Config Feature Check: cxx11_hdr_mutex : no  
    - Boost.Config Feature Check: cxx11_hdr_tuple : no  
    - Boost.Config Feature Check: cxx11_lambdas : no  
    - Boost.Config Feature Check: cxx11_noexcept : no  
    - Boost.Config Feature Check: cxx11_nullptr : no  
    - Boost.Config Feature Check: cxx11_rvalue_references : no  
    - Boost.Config Feature Check: cxx11_template_aliases : no  
    - Boost.Config Feature Check: cxx11_thread_local : no  
    - Boost.Config Feature Check: cxx11_variadic_templates : no  
    - has_icu builds           : no  
warning: Graph library does not contain MPI-based parallel components.  
note: to enable them, add "using mpi ;" to your user-config.jam.  
note: to suppress this message, pass "--without-graph_parallel" to bjam.  
    - zlib                     : no  
    - bzip2                    : no  
    - lzma                     : no  
    - zstd                     : no  
    - iconv (libc)             : no  
    - iconv (separate)         : no  
    - icu                      : no  
    - icu (lib64)              : no  
    - native-atomic-int32-supported : no  
    - message-compiler         : no  
    - native-syslog-supported  : no  
    - pthread-supports-robust-mutexes : no  
    - compiler-supports-ssse3  : no  
    - compiler-supports-avx2   : no  
    - gcc visibility           : no  
    - long double support      : no  
warning: skipping optional Message Passing Interface (MPI) library.  
note: to enable MPI support, add "using mpi ;" to user-config.jam.  
note: to suppress this message, pass "--without-mpi" to bjam.  
note: otherwise, you can safely ignore this message.  
    - libbacktrace builds      : no  
    - addr2line builds         : no  
    - WinDbg builds            : no  
    - WinDbgCached builds      : no  
    - BOOST_COMP_GNUC >= 4.3.0 : no  
    - zlib                     : no  (cached)  
    - bzip2                    : no  (cached)  
    - lzma                     : no  (cached)  
    - zstd                     : no  (cached)  
    - C++11 mutex              : no  
    - Boost.Config Feature Check: cxx11_auto_declarations : no  
    - Boost.Config Feature Check: cxx11_constexpr : no  
    - Boost.Config Feature Check: cxx11_defaulted_functions : no  
    - Boost.Config Feature Check: cxx11_final : no  
    - Boost.Config Feature Check: cxx11_hdr_mutex : no  
    - Boost.Config Feature Check: cxx11_hdr_tuple : no  
    - Boost.Config Feature Check: cxx11_lambdas : no  
    - Boost.Config Feature Check: cxx11_noexcept : no  
    - Boost.Config Feature Check: cxx11_nullptr : no  
    - Boost.Config Feature Check: cxx11_rvalue_references : no  
    - Boost.Config Feature Check: cxx11_template_aliases : no  
    - Boost.Config Feature Check: cxx11_thread_local : no  
    - Boost.Config Feature Check: cxx11_variadic_templates : no  
    - has_icu builds           : no  
    - zlib                     : no  
    - bzip2                    : no  
    - lzma                     : no  
    - zstd                     : no  
    - iconv (libc)             : no  
    - iconv (separate)         : no  
    - icu                      : no  
    - icu (lib64)              : no  
    - native-atomic-int32-supported : no  
    - message-compiler         : no  
    - native-syslog-supported  : no  
    - pthread-supports-robust-mutexes : no  
    - compiler-supports-ssse3  : no  
    - compiler-supports-avx2   : no  
    - gcc visibility           : no  
    - long double support      : no  
    - libbacktrace builds      : no  
    - addr2line builds         : no  
    - WinDbg builds            : no  
    - WinDbgCached builds      : no  
    - BOOST_COMP_GNUC >= 4.3.0 : no  
    - zlib                     : no  (cached)  
    - bzip2                    : no  (cached)  
    - lzma                     : no  (cached)  
    - zstd                     : no  (cached)  
    - C++11 mutex              : no  
    - Boost.Config Feature Check: cxx11_auto_declarations : no  
    - Boost.Config Feature Check: cxx11_constexpr : no  
    - Boost.Config Feature Check: cxx11_defaulted_functions : no  
    - Boost.Config Feature Check: cxx11_final : no  
    - Boost.Config Feature Check: cxx11_hdr_mutex : no  
    - Boost.Config Feature Check: cxx11_hdr_tuple : no  
    - Boost.Config Feature Check: cxx11_lambdas : no  
    - Boost.Config Feature Check: cxx11_noexcept : no  
    - Boost.Config Feature Check: cxx11_nullptr : no  
    - Boost.Config Feature Check: cxx11_rvalue_references : no  
    - Boost.Config Feature Check: cxx11_template_aliases : no  
    - Boost.Config Feature Check: cxx11_thread_local : no  
    - Boost.Config Feature Check: cxx11_variadic_templates : no  
    - has_icu builds           : no  
    - zlib                     : no  
    - bzip2                    : no  
    - lzma                     : no  
    - zstd                     : no  
    - iconv (libc)             : no  
    - iconv (separate)         : no  
    - icu                      : no  
    - icu (lib64)              : no  
    - native-atomic-int32-supported : no  
    - message-compiler         : no  
    - native-syslog-supported  : no  
    - pthread-supports-robust-mutexes : no  
    - compiler-supports-ssse3  : yes  
    - compiler-supports-avx2   : yes  
    - gcc visibility           : no  
    - long double support      : no  
    - libbacktrace builds      : no  
    - addr2line builds         : no  
    - WinDbg builds            : yes  
    - WinDbgCached builds      : no  
    - BOOST_COMP_GNUC >= 4.3.0 : no  
    - zlib                     : no  (cached)  
    - bzip2                    : no  (cached)  
    - lzma                     : no  (cached)  
    - zstd                     : no  (cached)  
    - C++11 mutex              : no  
    - Boost.Config Feature Check: cxx11_auto_declarations : no  
    - Boost.Config Feature Check: cxx11_constexpr : no  
    - Boost.Config Feature Check: cxx11_defaulted_functions : no  
    - Boost.Config Feature Check: cxx11_final : no  
    - Boost.Config Feature Check: cxx11_hdr_mutex : no  
    - Boost.Config Feature Check: cxx11_hdr_tuple : no  
    - Boost.Config Feature Check: cxx11_lambdas : no  
    - Boost.Config Feature Check: cxx11_noexcept : no  
    - Boost.Config Feature Check: cxx11_nullptr : no  
    - Boost.Config Feature Check: cxx11_rvalue_references : no  
    - Boost.Config Feature Check: cxx11_template_aliases : no  
    - Boost.Config Feature Check: cxx11_thread_local : no  
    - Boost.Config Feature Check: cxx11_variadic_templates : no  
    - has_icu builds           : no  
    - zlib                     : no  
    - bzip2                    : no  
    - lzma                     : no  
    - zstd                     : no  
    - iconv (libc)             : no  
    - iconv (separate)         : no  
    - icu                      : no  
    - icu (lib64)              : no  
    - native-atomic-int32-supported : no  
    - message-compiler         : no  
    - native-syslog-supported  : no  
    - pthread-supports-robust-mutexes : no  
    - compiler-supports-ssse3  : yes  
    - compiler-supports-avx2   : yes  
    - gcc visibility           : no  
    - long double support      : no  
    - libbacktrace builds      : no  
    - addr2line builds         : no  
    - WinDbg builds            : yes  
    - WinDbgCached builds      : no  
    - BOOST_COMP_GNUC >= 4.3.0 : no  
    - zlib                     : no  (cached)  
    - bzip2                    : no  (cached)  
    - lzma                     : no  (cached)  
    - zstd                     : no  (cached)  
error: Name clash for '<pC:\Working\Repositories\boost\stage\lib>libboost_atomic-clang  
error:  
error: Tried to build the target twice, with property sets having  
error: these incompatible properties:  
error:  
error:     -  <cxxstd>03  
error:     -  <cxxstd>11  
error:  
error: Please make sure to have consistent requirements for these  
error: properties everywhere in your project, especially for install  
error: targets.  
```  
  
</details>  
  
The problem seems to be that for some reason staging is happening even if I do not supply `stage` to b2, with MSVC the problem does not show up because library names contain VC version.

---

## Comment 10

> Username: pdimov  
> Created at: 2019-03-12 18:30:47 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-472127264  

`stage` is default from top level.

---

## Comment 11

> Username: Kojoley  
> Created at: 2019-03-12 18:35:28 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-472128991  

> `stage` is default from top level.  
  
Interesting. I do not understand why. I searched for option to disable staging and did not find it.

---

## Comment 12

> Username: grafikrobot  
> Created at: 2019-03-12 18:38:18 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-472130034  

@Kojoley you also don't need to build anything at the top level if what you are doing is running tests from the status dir.

---

## Comment 13

> Username: Kojoley  
> Created at: 2019-03-12 18:47:10 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-472133350  

> @Kojoley you also don't need to build anything at the top level if what you are doing is running tests from the status dir.  
  
Look, I do this `b2 toolset=$TOOLSET cxxstd=$CXXSTD warnings=on warnings-as-errors=on libs/spirit/test` and I am getting errors from `.cpp` files from Thread and other libraries, because of that I have to make `b2 toolset=$TOOLSET cxxstd=$CXXSTD warnings=off` before running the tests. Now I want to test multiple toolsets/stdcxxs simultaniously within one travis job, I of course can do `for CURSTDXX in ${CXXSTD//,/$IFS}; do ./b2 toolset=$TOOLSET cxxstd=$CURSTDXX warnings=off; done` but it is ugly.

---

## Comment 14

> Username: pdimov  
> Created at: 2019-03-12 18:51:28 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-472134861  

I'd tell you to use --layout=versioned but cxxstd doesn't get encoded. :-/

---

## Comment 15

> Username: swatanabe  
> Created at: 2019-03-12 19:06:39 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-472140182  

AMDG  
  
On 3/12/19 12:47 PM, Nikita Kniazev wrote:  
>> @Kojoley you also don't need to build anything at the top level if what you are doing is running tests from the status dir.  
>   
> Look, I do this `b2 toolset=$TOOLSET cxxstd=$CXXSTD warnings=on warnings-as-errors=on libs/spirit/test` and I am getting errors from `.cpp` files from Thread and other libraries, because of that I have to make `b2 toolset=$TOOLSET cxxstd=$CXXSTD warnings=off` before running the tests. Now I want to test multiple toolsets/stdcxxs simultaniously within one travis job, I of course can do `for CURSTDXX in ${CXXSTD//,/$IFS}; do ./b2 toolset=$TOOLSET cxxstd=$CURSTDXX warnings=off; done` but it is ugly.  
>   
  
If you know which libraries you need, you can use:  
  
DEPLIBS=thread  
./b2 toolset=$TOOLSET cxxstd=$CXXSTD $DEPLIBS  
  
In Christ,  
Steven Watanabe

---

## Comment 16

> Username: grafikrobot  
> Created at: 2019-03-12 19:46:21 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-472153613  

```  
Coqui:develop grafik$ b2 toolset=gcc,clang gcc cxxstd=11,14 warnings=on warnings-as-errors=on libs/spirit/test -n -a  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
    - Boost.Config Feature Check: cxx14_decltype_auto : no  
    - Boost.Config Feature Check: cxx14_generic_lambdas : no  
    - Boost.Config Feature Check: cxx14_return_type_deduction : no  
    - Boost.Config Feature Check: cxx14_variable_templates : no  
    - Boost.Config Feature Check: cxx14_decltype_auto : no  
    - Boost.Config Feature Check: cxx14_generic_lambdas : no  
    - Boost.Config Feature Check: cxx14_return_type_deduction : no  
    - Boost.Config Feature Check: cxx14_variable_templates : no  
    - Boost.Config Feature Check: cxx14_decltype_auto : yes  
    - Boost.Config Feature Check: cxx14_generic_lambdas : yes  
    - Boost.Config Feature Check: cxx14_return_type_deduction : yes  
    - Boost.Config Feature Check: cxx14_variable_templates : yes  
    - Boost.Config Feature Check: cxx14_decltype_auto : yes  
    - Boost.Config Feature Check: cxx14_generic_lambdas : yes  
    - Boost.Config Feature Check: cxx14_return_type_deduction : yes  
    - Boost.Config Feature Check: cxx14_variable_templates : yes  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - Boost.Config Feature Check: cxx14_decltype_auto : no  
    - Boost.Config Feature Check: cxx14_generic_lambdas : no  
    - Boost.Config Feature Check: cxx14_return_type_deduction : no  
    - Boost.Config Feature Check: cxx14_variable_templates : no  
    - Boost.Config Feature Check: cxx14_decltype_auto : no  
    - Boost.Config Feature Check: cxx14_generic_lambdas : no  
    - Boost.Config Feature Check: cxx14_return_type_deduction : no  
    - Boost.Config Feature Check: cxx14_variable_templates : no  
    - Boost.Config Feature Check: cxx14_decltype_auto : yes  
    - Boost.Config Feature Check: cxx14_generic_lambdas : yes  
    - Boost.Config Feature Check: cxx14_return_type_deduction : yes  
    - Boost.Config Feature Check: cxx14_variable_templates : yes  
    - Boost.Config Feature Check: cxx14_decltype_auto : yes  
    - Boost.Config Feature Check: cxx14_generic_lambdas : yes  
    - Boost.Config Feature Check: cxx14_return_type_deduction : yes  
    - Boost.Config Feature Check: cxx14_variable_templates : yes  
...patience...  
...found 10189 targets...  
...updating 8368 targets...  
common.mkdir bin.v2/libs/spirit  
  
        mkdir -p "bin.v2/libs/spirit"  
      
common.mkdir bin.v2/libs/spirit/test  
[snip]  
  
...updated 8368 targets...  
Coqui:develop grafik$   
```  
  
That's without actually building. Hence the problem is not the build system, AFAICT. But to be sure.. Here's what I get when building the above:  
  
```  
Coqui:develop grafik$ b2 toolset=gcc,clang gcc cxxstd=11,14 warnings=on warnings-as-errors=on libs/spirit/test -a -j8  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
    - Boost.Config Feature Check: cxx14_decltype_auto : no  
    - Boost.Config Feature Check: cxx14_generic_lambdas : no  
    - Boost.Config Feature Check: cxx14_return_type_deduction : no  
    - Boost.Config Feature Check: cxx14_variable_templates : no  
    - Boost.Config Feature Check: cxx14_decltype_auto : no  
    - Boost.Config Feature Check: cxx14_generic_lambdas : no  
    - Boost.Config Feature Check: cxx14_return_type_deduction : no  
    - Boost.Config Feature Check: cxx14_variable_templates : no  
    - Boost.Config Feature Check: cxx14_decltype_auto : yes  
    - Boost.Config Feature Check: cxx14_generic_lambdas : yes  
    - Boost.Config Feature Check: cxx14_return_type_deduction : yes  
    - Boost.Config Feature Check: cxx14_variable_templates : yes  
    - Boost.Config Feature Check: cxx14_decltype_auto : yes  
    - Boost.Config Feature Check: cxx14_generic_lambdas : yes  
    - Boost.Config Feature Check: cxx14_return_type_deduction : yes  
    - Boost.Config Feature Check: cxx14_variable_templates : yes  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - Boost.Config Feature Check: cxx14_decltype_auto : no  
    - Boost.Config Feature Check: cxx14_generic_lambdas : no  
    - Boost.Config Feature Check: cxx14_return_type_deduction : no  
    - Boost.Config Feature Check: cxx14_variable_templates : no  
    - Boost.Config Feature Check: cxx14_decltype_auto : no  
    - Boost.Config Feature Check: cxx14_generic_lambdas : no  
    - Boost.Config Feature Check: cxx14_return_type_deduction : no  
    - Boost.Config Feature Check: cxx14_variable_templates : no  
    - Boost.Config Feature Check: cxx14_decltype_auto : yes  
    - Boost.Config Feature Check: cxx14_generic_lambdas : yes  
    - Boost.Config Feature Check: cxx14_return_type_deduction : yes  
    - Boost.Config Feature Check: cxx14_variable_templates : yes  
    - Boost.Config Feature Check: cxx14_decltype_auto : yes  
    - Boost.Config Feature Check: cxx14_generic_lambdas : yes  
    - Boost.Config Feature Check: cxx14_return_type_deduction : yes  
    - Boost.Config Feature Check: cxx14_variable_templates : yes  
...patience...  
...found 10189 targets...  
...updating 8368 targets...  
[snip]  
**passed** bin.v2/libs/spirit/test/x3/x3_attribute_type_check.test/clang-darwin-10.0/debug/cxxstd-14-iso/threading-multi/visibility-hidden/x3_attribute_type_check.test  
...failed updating 6 targets...  
...skipped 18 targets...  
...updated 8442 targets...  
Coqui:develop grafik$   
```  
  
The failed targets are all Spirit tests failing for some reason or other. Hence AFAICT it's all working as expected.

---

## Comment 17

> Username: grafikrobot  
> Created at: 2019-03-12 19:48:11 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-472154266  

Do you have a user-config.jam or project-config.jam declaring the toolsets?

---

## Comment 18

> Username: Kojoley  
> Created at: 2019-03-12 19:52:06 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-472155607  

> Do you have a user-config.jam or project-config.jam declaring the toolsets?  
  
https://github.com/boostorg/spirit/blob/5a80befedd5f9821d0d0ea890a7edd6d84d5a561/.travis.yml#L83-L124  
  
  
> If you know which libraries you need, you can use:  
>   
> DEPLIBS=thread  
> ./b2 toolset=$TOOLSET cxxstd=$CXXSTD $DEPLIBS  
  
This seems to be an ok workaround, thanks.

---

## Comment 19

> Username: swatanabe  
> Created at: 2019-04-01 18:07:26 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-478684389  

This is incorrect usage, not a bug.

---

## Comment 20

> Username: Kojoley  
> Created at: 2021-03-14 23:42:23 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-799003068  

I thought it might be useful to someone how I finally solved the issue. I got tired of the workaround and luckily found a fix for my use case: alias a dependency with turned off warnings `alias boost_thread : /boost/thread//boost_thread : <warnings>off <warnings-as-errors>off ;` and consume the alias instead. This is still not ideal, as some libraries for whatever reason add warnings flags directly to `cxxflags`, but it made my life much easier. I think user-consumable libraries should override with `<warnings>off <warnings-as-errors>off` by default.

---

## Comment 21

> Username: pdimov  
> Created at: 2021-03-15 00:01:26 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-799006457  

You can also link to `/boost//thread/<warnings>off` instead of `/boost//thread`, which avoids the need for an alias. E.g. https://github.com/pdimov/describe/blob/acf14803ba027068b36e3ee36fe26b769aca81b1/test/Jamfile#L44-L45

---

## Comment 22

> Username: Kojoley  
> Created at: 2021-03-15 00:14:38 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-799009249  

I remember I saw this syntax but forgot about its existence. Is there a way to have multiple properties like that?  As I said above I have to do `<warnings-as-errors>off` too because of some libraries. The documentation seems to suggest yes, but it is unclear on how https://boostorg.github.io/build/manual/develop/index.html#bbv2.reference.targets.references, and there are examples only with a single property.

---

## Comment 23

> Username: pdimov  
> Created at: 2021-03-15 00:40:12 UTC  
> Url: https://github.com/boostorg/build/issues/399#issuecomment-799016236  

I think that you just add the additional properties the same way, e.g. `/boost//thread/<warnings>off/<warnings-as-errors>off`.  
  
(The documentation says `property-path` but never defines it.)
