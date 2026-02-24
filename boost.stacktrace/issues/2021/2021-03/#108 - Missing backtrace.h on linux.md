# #108 - Missing backtrace.h on linux [Closed]

> Username: opelx  
> Created at: 2021-03-12 19:24:57 UTC  
> Updated at: 2024-07-11 22:11:52 UTC  
> Closed at: 2021-04-17 05:56:42 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/108  

Compiling stacktrace on Linux (here fedora33 witj Clang 11) fails due to missing header backtrace.h. The [official documentation](https://www.boost.org/doc/libs/1_75_0/doc/html/stacktrace/configuration_and_build.html) states that there shall be a header:  
  
    -DBOOST_STACKTRACE_BACKTRACE_INCLUDE_FILE=</usr/lib/gcc/x86_64-linux-gnu/5/include/backtrace.h>  
  
but there isn't:  
  
    $ ls -la /usr/lib/gcc/x86_64-redhat-linux/10/include/stack*  
    ls: cannot access '/usr/lib/gcc/x86_64-redhat-linux/10/include/stack*': No such file or directory  
  
It seems, that this statement is old, a similar problem is shown [FS#50262 - [gcc] GCC ships without libbacktrace](https://bugs.archlinux.org/task/50262). My package system doesn't show any additional backtrace or stacktrace packages related to gcc. ```man backtrace``` refers to <execinfo.h>.   
  
What is the state on linux this time? Or miss I something?

---

## Comment 1

> Username: apolukhin  
> Created at: 2021-03-28 06:48:24 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/108#issuecomment-808856234  

If your Linux distro does not have a package with `backtrace.h` header then you have to manually install the libbacktrace from sources. References to that library are awailable on the same [docs page](https://www.boost.org/doc/libs/1_75_0/doc/html/stacktrace/configuration_and_build.html)

---

## Comment 2

> Username: opelx  
> Created at: 2021-04-10 18:23:26 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/108#issuecomment-817182971  

Thanks for your answer. I'm using CMake with FindBoost which fails to find backtrace.h. This library hasn't CMake support (probably will never get) out of the box. I have to build it into 3rd party dependency. All this results into an effort, which may comes obsolete since the offical linux libc way seems to be to use <execinfo.h>. Are there any plans to support this?

---

## Comment 3

> Username: apolukhin  
> Created at: 2021-04-10 22:24:30 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/108#issuecomment-817210297  

`backtrace_symbols` from `execinfo.h` just uses `dlsym` without demangling C++ symbols https://code.woboq.org/userspace/glibc/debug/backtracesyms.c.html  
  
In other words, it is slightly worse than the default **boost_stacktrace_basic** https://www.boost.org/doc/libs/1_75_0/doc/html/stacktrace/configuration_and_build.html

---

## Comment 4

> Username: major-mayer  
> Created at: 2021-04-13 15:52:03 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/108#issuecomment-818845270  

If anybody encounters this problem on Arch Linux or any of it's derivatives, you can find libbacktrace in the AUR: https://aur.archlinux.org/packages/libbacktrace-git

---

## Comment 5

> Username: opelx  
> Created at: 2021-04-17 05:56:35 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/108#issuecomment-821772843  

Thank you. I close the issue. I have to include lib backtrace into my build process somehow if I want to get better traces than boost_stacktrace_basic.

---

## Comment 6

> Username: zaufi  
> Created at: 2022-09-07 18:30:46 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/108#issuecomment-1239735438  

> `backtrace_symbols` from `execinfo.h` just uses `dlsym` without demangling C++ symbols https://code.woboq.org/userspace/glibc/debug/backtracesyms.c.html  
>   
> In other words, it is slightly worse than the default **boost_stacktrace_basic** https://www.boost.org/doc/libs/1_75_0/doc/html/stacktrace/configuration_and_build.html  
  
But with [`__cxa_demangle`](https://gcc.gnu.org/onlinedocs/libstdc++/manual/ext_demangling.html) it's very nice! At least for GCC... It'll be nice to have it as another alternative in the Boost::stacktrace library, please ;)

---

## Comment 7

> Username: justusranvier  
> Created at: 2023-07-21 14:51:27 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/108#issuecomment-1645714893  

I found my way to this issue after discovering that CMake's [FindBacktrace](https://cmake.org/cmake/help/latest/module/FindBacktrace.html) module points at ```<execinfo.h>``` and libbacktrace is not available for me to install.  
  
An implementation that uses ```<execinfo.h>``` and ```__cxa_demangle``` would be very welcome improvement over the ```boost_stacktrace_basic``` implementation.

---

## Comment 8

> Username: OrangeDog  
> Created at: 2024-07-11 22:11:51 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/108#issuecomment-2224031286  

Also with CMake, I had the problem of `find_path` not searching the internal include paths.  
This can help with that:  
  
```cmake  
find_path(BACKTRACE_INCLUDE backtrace.h  
        HINTS ${CMAKE_EXTRA_GENERATOR_CXX_SYSTEM_INCLUDE_DIRS}  
        REQUIRED)  
```  
giving e.g.  
```  
BACKTRACE_INCLUDE:PATH=/usr/lib/gcc/x86_64-linux-gnu/12/include  
```
