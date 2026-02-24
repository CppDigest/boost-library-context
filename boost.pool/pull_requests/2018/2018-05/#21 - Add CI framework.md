# #21 Add CI framework [Merged]

> Username: jeking3  
> Created at: 2018-05-14 12:06:20 UTC  
> Updated at: 2018-05-20 03:58:42 UTC  
> Merged at: 2018-05-19 17:53:06 UTC  
> Closed at: 2018-05-19 17:53:07 UTC  
> Url: https://github.com/boostorg/pool/pull/21  

Add CI framework used in a number of other repositories:  
  - travis with valgrind, cppcheck, ubsan, codecov, covscan (future)  
  - appveyor with MSVC 2010 through 2017, cygwin 32/64, mingw 32/64  
  - README, LICENSE, etc.  
  
### Disabled ###  
  - ubsan in Travis (see https://github.com/boostorg/pool/issues/19)  
  
### Example Builds ###  
AppVeyor: https://ci.appveyor.com/project/jeking3/pool/build/1.0.12-develop  
Travis: https://travis-ci.org/jeking3/pool/builds/378543940

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-05-14 12:20:54 UTC  
> Updated_at: 2018-05-19 01:12:45 UTC  
> Url: https://github.com/boostorg/pool/pull/21#issuecomment-388797338  

# [Codecov](https://codecov.io/gh/boostorg/pool/pull/21?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@677d2fb`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/pool/pull/21/graphs/tree.svg?token=blmk8CZxg2&src=pr&height=150&width=650)](https://codecov.io/gh/boostorg/pool/pull/21?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #21   +/-   ##  
==========================================  
  Coverage           ?   70.41%             
==========================================  
  Files              ?        8             
  Lines              ?      436             
  Branches           ?      136             
==========================================  
  Hits               ?      307             
  Misses             ?       28             
  Partials           ?      101  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/pool/pull/21?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/pool/pull/21?src=pr&el=footer). Last update [677d2fb...dc723ad](https://codecov.io/gh/boostorg/pool/pull/21?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-05-16 01:21:41 UTC  
> Url: https://github.com/boostorg/pool/pull/21#issuecomment-389364466  

@mclow this is ready for review/merge.  Someone needs to enable an AppVeyor build on this repository.  If you can make me a collaborator on this repository (like uuid/format/date_time) I can set it up to run on each PR.

---

## Comment 3

> Username: mclow  
> Created_at: 2018-05-19 17:53:23 UTC  
> Url: https://github.com/boostorg/pool/pull/21#issuecomment-390421713  

Let's watch how this goes, and then (Monday?) merge all the others as well.

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-05-19 22:48:13 UTC  
> Url: https://github.com/boostorg/pool/pull/21#issuecomment-390437656  

Thanks - after I identified a generic issue with cygwin and Boost.Test, I re-enabled cygwin for this PR and it exposed another issue which is currently failing:  
```  
./boost/thread/lock_guard.hpp:65:40: warning: invoking macro BOOST_THREAD_RELEASE argument 1: empty macro arguments are undefined in ISO C++98 [-Wpedantic]  
     ~lock_guard() BOOST_THREAD_RELEASE()  
                                        ^  
libs\thread\src\win32\thread.cpp: In function 'void boost::{anonymous}::create_current_thread_tls_key()':  
libs\thread\src\win32\thread.cpp:86:37: error: 'tss_cleanup_implemented' was not declared in this scope  
             tss_cleanup_implemented(); // if anyone uses TSS, we need the cleanup linked in  
                                     ^  
    "g++"   -std=c++03 -m64 -mthreads -O3 -finline-functions -Wno-inline -Wall -pedantic -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_USES_CHRONO -DBOOST_THREAD_WIN32 -DNDEBUG -D_POSIX_C_SOURCE=200112L  -I"." -c -o "bin.v2\libs\thread\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\threadapi-win32\threading-multi\win32\thread.o" "libs\thread\src\win32\thread.cpp"  
```  
  
I don't see this in the other repositories so still need to figure this one out.

---
