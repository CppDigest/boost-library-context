# #263 - gcc-14: map_shadow_stack incorrect syscall number [Closed]

> Username: anarthal  
> Created at: 2024-07-08 10:13:05 UTC  
> Updated at: 2025-01-03 16:00:53 UTC  
> Closed at: 2024-12-26 07:09:08 UTC  
> Url: https://github.com/boostorg/context/issues/263  

System: Ubuntu 24.04 (as per the `ubuntu:24.04` Docker image)  
Compiler: g++-14 (as per `apt install g++-14` in the aforementioned image)  
uname -a: `Linux 2b064e2a8785 6.5.0-41-generic #41~22.04.2-Ubuntu SMP PREEMPT_DYNAMIC Mon Jun  3 11:32:55 UTC 2 x86_64 x86_64 x86_64 GNU/Linux`  
glibc version: 2.39  
Variant: release  
Library version: Boost develop branch  
  
I'm getting a warning about a redefinition of `__NR_map_shadow_stack`. It's not causing any issues, in part because I'm only indirectly including the file (via Asio's `spawn.hpp`). It looks like `<boost/context/fiber.hpp>`  is defining `__NR_map_shadow_stack` to 451, but the actual syscall number seems to be 453. This only happens in C++23, where `<memory>` includes all the syscall numbers.  
  
For me it's just an annoying warning, but it seems it may point to a real bug. I don't know enough to provide further diagnostics.  
  
Please note that this doesn't happen in the Godbolt environment, which uses an older glibc version. The ubuntu 24.04 gcc-14 seems to have `__CET__` defined by default.  
  
Repro instructions:  
  
```  
$> cd $BOOST_ROOT  
$> cat libs/mysql/repro/Jamfile  
exe repro :  
    repro.cpp  
    /boost/context//boost_context ;  
  
$> cat libs/mysql/repro/repro.cpp  
#include <boost/context/fiber.hpp>  
  
int main() {}  
  
$> ./b2 toolset=gcc-14 cxxstd=23 libs/mysql/repro warnings-as-errors=on  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
    - symlinks supported       : yes (cached)  
  
[1] gcc-14  
...patience...  
...found 639 targets...  
...updating 9 targets...  
gcc.compile.c++ bin.v2/libs/mysql/repro/gcc-14/debug/x86_64/cxxstd-23-iso/threading-multi/visibility-hidden/repro.o  
In file included from ./boost/context/fiber.hpp:12,  
                 from libs/mysql/repro/repro.cpp:1:  
./boost/context/fiber_fcontext.hpp:52:11: error: "__NR_map_shadow_stack" redefined [-Werror]  
   52 | #  define __NR_map_shadow_stack 451  
      |           ^~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/include/x86_64-linux-gnu/asm/unistd.h:20,  
                 from /usr/include/x86_64-linux-gnu/sys/syscall.h:24,  
                 from /usr/include/syscall.h:1,  
                 from /usr/include/c++/14/bits/atomic_wait.h:47,  
                 from /usr/include/c++/14/bits/atomic_base.h:42,  
                 from /usr/include/c++/14/bits/shared_ptr_atomic.h:33,  
                 from /usr/include/c++/14/memory:81,  
                 from ./boost/context/fiber_fcontext.hpp:18:  
/usr/include/x86_64-linux-gnu/asm/unistd_64.h:368:9: note: this is the location of the previous definition  
  368 | #define __NR_map_shadow_stack 453  
      |         ^~~~~~~~~~~~~~~~~~~~~  
cc1plus: all warnings being treated as errors  
  
    "g++"   -std=c++23 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -Werror -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_DYN_LINK=1   -I"."  -c -o "bin.v2/libs/mysql/repro/gcc-14/debug/x86_64/cxxstd-23-iso/threading-multi/visibility-hidden/repro.o" "libs/mysql/repro/repro.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/mysql/repro/gcc-14/debug/x86_64/cxxstd-23-iso/threading-multi/visibility-hidden/repro.o...  
...skipped <pbin.v2/libs/mysql/repro/gcc-14/debug/x86_64/cxxstd-23-iso/threading-multi/visibility-hidden>repro for lack of <pbin.v2/libs/mysql/repro/gcc-14/debug/x86_64/cxxstd-23-iso/threading-multi/visibility-hidden>repro.o...  
  
...updated 7 targets...  
  
...skipped 1 target...  
   <pbin.v2/libs/mysql/repro/gcc-14/debug/x86_64/cxxstd-23-iso/threading-multi/visibility-hidden>repro  
  
...failed updating 1 target...  
   gcc.compile.c++ bin.v2/libs/mysql/repro/gcc-14/debug/x86_64/cxxstd-23-iso/threading-multi/visibility-hidden/repro.o  
```

---

## Comment 1

> Username: olk  
> Created at: 2024-08-28 10:43:05 UTC  
> Url: https://github.com/boostorg/context/issues/263#issuecomment-2314964354  

Might be fixed by PR 259 (https://github.com/boostorg/context/pull/259). Could you test it?

---

## Comment 2

> Username: arnaud-lb  
> Created at: 2024-08-28 16:05:38 UTC  
> Url: https://github.com/boostorg/context/issues/263#issuecomment-2315752841  

This is a separate issue.  
  
The syscall number appears to have changed since the implementation of shadow stacks in boost/context.  
  
The impact is that boost/context will fail to setup a shadow stack when SHSTK is enabled (which is unlikely currently), and will crash.  
  
In https://github.com/php/php-src/pull/14027/files we define `SYS_map_shadow_stack` to 453, and only when it's not already defined.

---

## Comment 3

> Username: anarthal  
> Created at: 2024-09-01 13:31:39 UTC  
> Url: https://github.com/boostorg/context/issues/263#issuecomment-2323342101  

Confirming that this still happens, and having read the source, I agree with @arnaud-lb on the diagnostics. I don't know the specifics on gcc-14 and why I'm encountering this without setting any flags, but this looks like a problem.

---

## Comment 4

> Username: anarthal  
> Created at: 2025-01-03 10:37:04 UTC  
> Url: https://github.com/boostorg/context/issues/263#issuecomment-2569019358  

Confirming that this solved the issue. Thanks.

---

## Comment 5

> Username: olk  
> Created at: 2025-01-03 16:00:51 UTC  
> Url: https://github.com/boostorg/context/issues/263#issuecomment-2569462163  

> Confirming that this solved the issue. Thanks.  
  
ty
