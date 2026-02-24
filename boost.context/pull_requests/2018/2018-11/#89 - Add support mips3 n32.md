# #89 Add support mips3 n32 [Closed]

> Username: emtone  
> Created at: 2018-11-06 16:17:58 UTC  
> Updated at: 2021-08-06 05:43:23 UTC  
> Closed at: 2021-08-06 05:43:23 UTC  
> Url: https://github.com/boostorg/context/pull/89  

What the assembler files used for? I have the loongson2F user guide, but I don't known how to use it for these 3  assembler file.

---

## Comment 1

> Username: olk  
> Created_at: 2018-11-07 10:29:36 UTC  
> Updated_at: 2018-11-07 10:52:00 UTC  
> Url: https://github.com/boostorg/context/pull/89#issuecomment-436578366  

The assembler files of N32 are identical to the O32 ones ... this will not work, the assembler must be adopted to N32.

---

## Comment 2

> Username: emtone  
> Created_at: 2018-11-11 16:50:39 UTC  
> Url: https://github.com/boostorg/context/pull/89#issuecomment-437685654  

OK, I will try to fix this.

---

## Review 3 [Commented]

> Username: FlyGoat  
> Created_at: 2018-11-28 07:24:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/context/pull/89#pullrequestreview-179151195  

📁 src/asm/jump_mips32_n32_elf_gas.S

```diff
  48 |+  *  | 160 | 164 | 168 | 172 | 176 | 180 | 184 | 188 |  *
  49 |+  *  -------------------------------------------------  *
  50 |+  *  |    DATA   |                                   |  *
```

> Username: FlyGoat  
> Created_at: 2018-11-28 07:24:49 UTC  
> Updated_at: 2018-11-28 15:25:59 UTC  
> Url: https://github.com/boostorg/context/pull/89#discussion_r236964767  

It seems like N32 ABI doesn't need these sections since the pointers will be passed by registers rather than in stack.


---

## Review 4 [Commented]

> Username: FlyGoat  
> Created_at: 2018-11-28 07:25:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/context/pull/89#pullrequestreview-179151428  

📁 src/asm/jump_mips32_n32_elf_gas.S

```diff
  60 |-     sw  $ra, 88($sp)  # save RA
  61 |-     sw  $ra, 92($sp)  # save RA as PC
  64 |+     s.d  $s0, 48($sp)  # save S0
```

> Username: FlyGoat  
> Created_at: 2018-11-28 07:25:47 UTC  
> Updated_at: 2018-11-28 15:25:59 UTC  
> Url: https://github.com/boostorg/context/pull/89#discussion_r236965074  

It would be better to use sd for general registers.


---

## Comment 5

> Username: FlyGoat  
> Created_at: 2018-11-28 07:26:41 UTC  
> Updated_at: 2018-11-28 08:11:16 UTC  
> Url: https://github.com/boostorg/context/pull/89#issuecomment-442346535  

And you forget to add it into Jamfile... #Edit: My fault.. In the previous commit.  
I'm doubting if this code really works.

---

## Comment 6

> Username: emtone  
> Created_at: 2018-11-28 07:54:28 UTC  
> Url: https://github.com/boostorg/context/pull/89#issuecomment-442352661  

Hi FlyGoat,  
Thanks for you reply. I will check the problem and run test.  
BTW, The n32 files had add to the Jamfile, please check.

---

## Comment 7

> Username: FlyGoat  
> Created_at: 2018-11-28 08:15:22 UTC  
> Url: https://github.com/boostorg/context/pull/89#issuecomment-442357951  

Here is a blog post by heiher in Chinese,  
https://hev.cc/2570.html  
It may be easier for you to understand how it works.

---

## Comment 8

> Username: emtone  
> Created_at: 2018-11-28 08:24:58 UTC  
> Url: https://github.com/boostorg/context/pull/89#issuecomment-442360385  

Ok, I will read it, Thank you!

---

## Comment 9

> Username: FlyGoat  
> Created_at: 2018-11-29 01:19:01 UTC  
> Url: https://github.com/boostorg/context/pull/89#issuecomment-442668941  

I'm going to work on N32 support later, probably next month.  
It would be nice if you can give me remote access to a machine running N32 OS, because all of my Loongson-2F machines are running O32 OS...

---

## Comment 10

> Username: FlyGoat  
> Created_at: 2018-11-29 06:48:45 UTC  
> Url: https://github.com/boostorg/context/pull/89#issuecomment-442725100  

Hi there,  
Just make an implementation for N32 ABI https://github.com/FlyGoat/context/tree/mips-n32  
But I can't test. Please test if it's possible, I'll purpose a PR if it passes the test.

---

## Comment 11

> Username: emtone  
> Created_at: 2018-11-29 14:12:01 UTC  
> Url: https://github.com/boostorg/context/pull/89#issuecomment-442847053  

I test it, here is the result:  
```  
yeeloong /var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32 # b2 --user-config=/var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32/user-config.jam gentoorelease -j1 -q -d+2 pch=off --disable-icu boost.locale.icu=off --without-mpi --boost-build=/usr/share/boost-build --prefix=/var/tmp/portage/dev-libs/boost-1.67.0/image/usr --layout=system threading=multi link=shared --python-buildid=2.7 abi=n32 architecture=mips1   
/var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32/libs/predef/check/../tools/check/predef.jam:47: Unescaped special character in argument $(language)::$(expression)  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : none (cached)  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes (cached)  
    - C++11 mutex              : yes  
    - lockfree boost::atomic_flag : yes  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes  
    - Boost.Config Feature Check: cxx11_constexpr : yes  
    - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
    - Boost.Config Feature Check: cxx11_final : yes  
    - Boost.Config Feature Check: cxx11_hdr_mutex : yes  
    - Boost.Config Feature Check: cxx11_hdr_regex : yes  
    - Boost.Config Feature Check: cxx11_hdr_tuple : yes  
    - Boost.Config Feature Check: cxx11_lambdas : yes  
    - Boost.Config Feature Check: cxx11_noexcept : yes  
    - Boost.Config Feature Check: cxx11_nullptr : yes  
    - Boost.Config Feature Check: cxx11_rvalue_references : yes  
    - Boost.Config Feature Check: cxx11_template_aliases : yes  
    - Boost.Config Feature Check: cxx11_thread_local : yes  
    - Boost.Config Feature Check: cxx11_variadic_templates : yes  
    - has_icu builds           : no  
warning: Graph library does not contain MPI-based parallel components.  
note: to enable them, add "using mpi ;" to your user-config.jam  
    - zlib                     : yes  
    - bzip2                    : yes  
    - lzma                     : yes  
    - iconv (libc)             : yes  
    - native-atomic-int32-supported : yes  
    - native-syslog-supported  : yes  
    - pthread-supports-robust-mutexes : yes  
    - compiler-supports-visibility : yes  
    - gcc visibility           : yes  
    - long double support      : yes  
    - libbacktrace builds      : no  
    - addr2line builds         : yes  
    - WinDbg builds            : no  
    - WinDbgCached builds      : no  
    - BOOST_COMP_GNUC >= 4.3.0 : yes  
  
Component configuration:  
  
    - atomic                   : building  
    - chrono                   : building  
    - container                : building  
    - context                  : building  
    - contract                 : building  
    - coroutine                : building  
    - date_time                : building  
    - exception                : building  
    - fiber                    : building  
    - filesystem               : building  
    - graph                    : building  
    - graph_parallel           : building  
    - iostreams                : building  
    - locale                   : building  
    - log                      : building  
    - math                     : building  
    - mpi                      : not building  
    - program_options          : building  
    - python                   : building  
    - random                   : building  
    - regex                    : building  
    - serialization            : building  
    - signals                  : building  
    - stacktrace               : building  
    - system                   : building  
    - test                     : building  
    - thread                   : building  
    - timer                    : building  
    - type_erasure             : building  
    - wave                     : building  
  
common.mkdir bin.v2/libs/atomic/build/gcc-8.2/gentoorelease/architecture-mips1  
  
        mkdir -p "bin.v2/libs/atomic/build/gcc-8.2/gentoorelease/architecture-mips1"  
      
common.mkdir bin.v2/libs/atomic/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off  
  
        mkdir -p "bin.v2/libs/atomic/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off"  
      
common.mkdir bin.v2/libs/atomic/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi  
  
        mkdir -p "bin.v2/libs/atomic/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi"  
      
gcc.compile.c++ bin.v2/libs/atomic/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/lockpool.o  
  
    "mips64el-unknown-linux-gnu-g++"   -O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe -std=c++14 -fPIC -pthread -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_ATOMIC_SOURCE -DNDEBUG  -I"." -c -o "bin.v2/libs/atomic/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/lockpool.o" "libs/atomic/src/lockpool.cpp"  
  
gcc.link.dll bin.v2/libs/atomic/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/libboost_atomic.so.1.67.0  
  
    "mips64el-unknown-linux-gnu-g++"    -o "bin.v2/libs/atomic/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/libboost_atomic.so.1.67.0" -Wl,-h -Wl,libboost_atomic.so.1.67.0 -shared -Wl,--start-group "bin.v2/libs/atomic/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/lockpool.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -pthread -Wl,-O1 -Wl,--as-needed   
  
common.copy stage/lib/libboost_atomic.so.1.67.0  
  
    cp "bin.v2/libs/atomic/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/libboost_atomic.so.1.67.0"  "stage/lib/libboost_atomic.so.1.67.0"  
  
ln-UNIX stage/lib/libboost_atomic.so  
  
    ln -f -s 'libboost_atomic.so.1.67.0' 'stage/lib/libboost_atomic.so'  
  
common.mkdir bin.v2/libs/system/build/gcc-8.2/gentoorelease/architecture-mips1  
  
        mkdir -p "bin.v2/libs/system/build/gcc-8.2/gentoorelease/architecture-mips1"  
      
common.mkdir bin.v2/libs/system/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off  
  
        mkdir -p "bin.v2/libs/system/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off"  
      
common.mkdir bin.v2/libs/system/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi  
  
        mkdir -p "bin.v2/libs/system/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi"  
      
gcc.compile.c++ bin.v2/libs/system/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/error_code.o  
  
    "mips64el-unknown-linux-gnu-g++"   -O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe -std=c++14 -fPIC -pthread -finline-functions -Wno-inline -Wall -pedantic  -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1 -DNDEBUG  -I"." -c -o "bin.v2/libs/system/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/error_code.o" "libs/system/src/error_code.cpp"  
  
gcc.link.dll bin.v2/libs/system/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/libboost_system.so.1.67.0  
  
    "mips64el-unknown-linux-gnu-g++"    -o "bin.v2/libs/system/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/libboost_system.so.1.67.0" -Wl,-h -Wl,libboost_system.so.1.67.0 -shared -Wl,--start-group "bin.v2/libs/system/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/error_code.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -pthread -Wl,-O1 -Wl,--as-needed   
  
common.copy stage/lib/libboost_system.so.1.67.0  
  
    cp "bin.v2/libs/system/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/libboost_system.so.1.67.0"  "stage/lib/libboost_system.so.1.67.0"  
  
ln-UNIX stage/lib/libboost_system.so  
  
    ln -f -s 'libboost_system.so.1.67.0' 'stage/lib/libboost_system.so'  
  
common.mkdir bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1  
  
        mkdir -p "bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1"  
      
common.mkdir bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off  
  
        mkdir -p "bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off"  
      
common.mkdir bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi  
  
        mkdir -p "bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi"  
      
gcc.compile.c++ bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/chrono.o  
  
    "mips64el-unknown-linux-gnu-g++"   -O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe -std=c++14 -fPIC -pthread -finline-functions -Wno-inline -Wall -pedantic -Wextra -Wno-long-long -DBOOST_ALL_DYN_LINK=1 -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_NO_DEPRECATED -DNDEBUG  -I"." -c -o "bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/chrono.o" "libs/chrono/src/chrono.cpp"  
  
gcc.compile.c++ bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/thread_clock.o  
  
    "mips64el-unknown-linux-gnu-g++"   -O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe -std=c++14 -fPIC -pthread -finline-functions -Wno-inline -Wall -pedantic -Wextra -Wno-long-long -DBOOST_ALL_DYN_LINK=1 -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_NO_DEPRECATED -DNDEBUG  -I"." -c -o "bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/thread_clock.o" "libs/chrono/src/thread_clock.cpp"  
  
gcc.compile.c++ bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/process_cpu_clocks.o  
  
    "mips64el-unknown-linux-gnu-g++"   -O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe -std=c++14 -fPIC -pthread -finline-functions -Wno-inline -Wall -pedantic -Wextra -Wno-long-long -DBOOST_ALL_DYN_LINK=1 -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_NO_DEPRECATED -DNDEBUG  -I"." -c -o "bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/process_cpu_clocks.o" "libs/chrono/src/process_cpu_clocks.cpp"  
  
gcc.link.dll bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/libboost_chrono.so.1.67.0  
  
    "mips64el-unknown-linux-gnu-g++"    -o "bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/libboost_chrono.so.1.67.0" -Wl,-h -Wl,libboost_chrono.so.1.67.0 -shared -Wl,--start-group "bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/chrono.o" "bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/thread_clock.o" "bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/process_cpu_clocks.o" "bin.v2/libs/system/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/libboost_system.so.1.67.0"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -pthread -Wl,-O1 -Wl,--as-needed -lrt -lpthread  
  
common.copy stage/lib/libboost_chrono.so.1.67.0  
  
    cp "bin.v2/libs/chrono/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/libboost_chrono.so.1.67.0"  "stage/lib/libboost_chrono.so.1.67.0"  
  
ln-UNIX stage/lib/libboost_chrono.so  
  
    ln -f -s 'libboost_chrono.so.1.67.0' 'stage/lib/libboost_chrono.so'  
  
common.mkdir bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1  
  
        mkdir -p "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1"  
      
common.mkdir bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off  
  
        mkdir -p "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off"  
      
common.mkdir bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi  
  
        mkdir -p "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi"  
      
gcc.compile.c bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/alloc_lib.o  
  
    "mips64el-unknown-linux-gnu-g++" -x c -O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe -fPIC -pthread -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTAINER_DYN_LINK=1 -DNDEBUG  -I"." -c -o "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/alloc_lib.o" "libs/container/src/alloc_lib.c"  
  
gcc.compile.c++ bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/dlmalloc.o  
  
    "mips64el-unknown-linux-gnu-g++"   -O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe -std=c++14 -fPIC -pthread -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTAINER_DYN_LINK=1 -DNDEBUG  -I"." -c -o "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/dlmalloc.o" "libs/container/src/dlmalloc.cpp"  
  
gcc.compile.c++ bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/global_resource.o  
  
    "mips64el-unknown-linux-gnu-g++"   -O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe -std=c++14 -fPIC -pthread -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTAINER_DYN_LINK=1 -DNDEBUG  -I"." -c -o "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/global_resource.o" "libs/container/src/global_resource.cpp"  
  
gcc.compile.c++ bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/monotonic_buffer_resource.o  
  
    "mips64el-unknown-linux-gnu-g++"   -O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe -std=c++14 -fPIC -pthread -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTAINER_DYN_LINK=1 -DNDEBUG  -I"." -c -o "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/monotonic_buffer_resource.o" "libs/container/src/monotonic_buffer_resource.cpp"  
  
gcc.compile.c++ bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/pool_resource.o  
  
    "mips64el-unknown-linux-gnu-g++"   -O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe -std=c++14 -fPIC -pthread -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTAINER_DYN_LINK=1 -DNDEBUG  -I"." -c -o "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/pool_resource.o" "libs/container/src/pool_resource.cpp"  
  
gcc.compile.c++ bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/synchronized_pool_resource.o  
  
    "mips64el-unknown-linux-gnu-g++"   -O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe -std=c++14 -fPIC -pthread -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTAINER_DYN_LINK=1 -DNDEBUG  -I"." -c -o "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/synchronized_pool_resource.o" "libs/container/src/synchronized_pool_resource.cpp"  
  
gcc.compile.c++ bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/unsynchronized_pool_resource.o  
  
    "mips64el-unknown-linux-gnu-g++"   -O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe -std=c++14 -fPIC -pthread -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTAINER_DYN_LINK=1 -DNDEBUG  -I"." -c -o "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/unsynchronized_pool_resource.o" "libs/container/src/unsynchronized_pool_resource.cpp"  
  
gcc.link.dll bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/libboost_container.so.1.67.0  
  
    "mips64el-unknown-linux-gnu-g++"    -o "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/libboost_container.so.1.67.0" -Wl,-h -Wl,libboost_container.so.1.67.0 -shared -Wl,--start-group "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/alloc_lib.o" "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/dlmalloc.o" "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/global_resource.o" "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/monotonic_buffer_resource.o" "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/pool_resource.o" "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/synchronized_pool_resource.o" "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/unsynchronized_pool_resource.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -pthread -Wl,-O1 -Wl,--as-needed   
  
common.copy stage/lib/libboost_container.so.1.67.0  
  
    cp "bin.v2/libs/container/build/gcc-8.2/gentoorelease/architecture-mips1/pch-off/threading-multi/libboost_container.so.1.67.0"  "stage/lib/libboost_container.so.1.67.0"  
  
ln-UNIX stage/lib/libboost_container.so  
  
    ln -f -s 'libboost_container.so.1.67.0' 'stage/lib/libboost_container.so'  
  
common.mkdir bin.v2/libs/context/build/gcc-8.2/gentoorelease/abi-n32  
  
        mkdir -p "bin.v2/libs/context/build/gcc-8.2/gentoorelease/abi-n32"  
      
common.mkdir bin.v2/libs/context/build/gcc-8.2/gentoorelease/abi-n32/architecture-mips1  
  
        mkdir -p "bin.v2/libs/context/build/gcc-8.2/gentoorelease/abi-n32/architecture-mips1"  
      
common.mkdir bin.v2/libs/context/build/gcc-8.2/gentoorelease/abi-n32/architecture-mips1/pch-off  
  
        mkdir -p "bin.v2/libs/context/build/gcc-8.2/gentoorelease/abi-n32/architecture-mips1/pch-off"  
      
common.mkdir bin.v2/libs/context/build/gcc-8.2/gentoorelease/abi-n32/architecture-mips1/pch-off/threading-multi  
  
        mkdir -p "bin.v2/libs/context/build/gcc-8.2/gentoorelease/abi-n32/architecture-mips1/pch-off/threading-multi"  
      
common.mkdir bin.v2/libs/context/build/gcc-8.2/gentoorelease/abi-n32/architecture-mips1/pch-off/threading-multi/asm  
  
        mkdir -p "bin.v2/libs/context/build/gcc-8.2/gentoorelease/abi-n32/architecture-mips1/pch-off/threading-multi/asm"  
      
gcc.compile.asm bin.v2/libs/context/build/gcc-8.2/gentoorelease/abi-n32/architecture-mips1/pch-off/threading-multi/asm/make_mips64_n32_elf_gas.o  
  
    "mips64el-unknown-linux-gnu-g++" -x assembler-with-cpp -fPIC -pthread -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_CONTEXT_SOURCE -DNDEBUG -I"." -c -o "bin.v2/libs/context/build/gcc-8.2/gentoorelease/abi-n32/architecture-mips1/pch-off/threading-multi/asm/make_mips64_n32_elf_gas.o" "libs/context/src/asm/make_mips64_n32_elf_gas.S"  
  
gcc.compile.asm bin.v2/libs/context/build/gcc-8.2/gentoorelease/abi-n32/architecture-mips1/pch-off/threading-multi/asm/jump_mips64_n32_elf_gas.o  
  
    "mips64el-unknown-linux-gnu-g++" -x assembler-with-cpp -fPIC -pthread -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_CONTEXT_SOURCE -DNDEBUG -I"." -c -o "bin.v2/libs/context/build/gcc-8.2/gentoorelease/abi-n32/architecture-mips1/pch-off/threading-multi/asm/jump_mips64_n32_elf_gas.o" "libs/context/src/asm/jump_mips64_n32_elf_gas.S"  
  
jump_mips64_n64_elf_gas.S: Assembler messages:  
jump_mips64_n64_elf_gas.S:83: Error: operand 3 out of range `srl $t0,$a0,32'  
jump_mips64_n64_elf_gas.S:84: Error: operand 3 out of range `sll $t0,$t0,32'  
jump_mips64_n64_elf_gas.S:87: Error: operand 3 out of range `sll $a0,$a0,32'  
jump_mips64_n64_elf_gas.S:88: Error: operand 3 out of range `srl $a0,$a0,32'  
...failed updating 1 target...  
yeeloong /var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32 #   
```

---

## Comment 12

> Username: neheb  
> Created_at: 2021-04-11 00:26:53 UTC  
> Url: https://github.com/boostorg/context/pull/89#issuecomment-817222549  

What happened with this?

---

## Comment 13

> Username: olk  
> Created_at: 2021-04-11 06:02:46 UTC  
> Url: https://github.com/boostorg/context/pull/89#issuecomment-817254709  

no progress  
  
Rosen Penev ***@***.***> schrieb am So., 11. Apr. 2021, 02:27:  
  
> What happened with this?  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/context/pull/89#issuecomment-817222549>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAG7IQFNTPYWC4ZNMHAQSU3TIDUFVANCNFSM4GCDEJ5A>  
> .  
>

---

## Comment 14

> Username: olk  
> Created_at: 2021-07-13 18:50:17 UTC  
> Url: https://github.com/boostorg/context/pull/89#issuecomment-879319801  

What's teh status of this PR? Does it work?

---

## Comment 15

> Username: olk  
> Created_at: 2021-08-06 05:43:23 UTC  
> Url: https://github.com/boostorg/context/pull/89#issuecomment-894016007  

closed in favor of 561a315

---
