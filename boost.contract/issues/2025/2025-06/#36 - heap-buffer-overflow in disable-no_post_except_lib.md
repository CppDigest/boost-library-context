# #36 - heap-buffer-overflow in disable-no_post_except_lib [Open]

> Username: jeking3  
> Created at: 2025-06-01 13:21:47 UTC  
> Updated at: 2025-06-08 16:14:07 UTC  
> Url: https://github.com/boostorg/contract/issues/36  

Due to this issue, a test was disabled so that CI can pass all other tests.  
  
Ubuntu 24 with clang 14  
  
only happens with the release build.  Changing visibility to global does not fix it.  Changing link to shared does.  
  
```  
...skipped 3 targets...  
   <pbin.v2/libs/contract/test/disable-no_post_except_lib.test/clang-linux-14/release/x86_64/address-sanitizer-norecover/cxxstd-11-iso/link-static/threading-multi/undefined-sanitizer-on/visibility-hidden>disable-no_post_except_lib.test  
   <pbin.v2/libs/contract/test/disable-no_post_except_lib.test/clang-linux-14/release/x86_64/address-sanitizer-norecover/cxxstd-17-iso/link-static/threading-multi/undefined-sanitizer-on/visibility-hidden>disable-no_post_except_lib.test  
   <pbin.v2/libs/contract/test/disable-no_post_except_lib.test/clang-linux-14/release/x86_64/address-sanitizer-norecover/cxxstd-20-iso/link-static/threading-multi/undefined-sanitizer-on/visibility-hidden>disable-no_post_except_lib.test  
  
...failed updating 3 targets...  
   testing.capture-output bin.v2/libs/contract/test/disable-no_post_except_lib.test/clang-linux-14/release/x86_64/address-sanitizer-norecover/cxxstd-11-iso/link-static/threading-multi/undefined-sanitizer-on/visibility-hidden/disable-no_post_except_lib.run  
   testing.capture-output bin.v2/libs/contract/test/disable-no_post_except_lib.test/clang-linux-14/release/x86_64/address-sanitizer-norecover/cxxstd-17-iso/link-static/threading-multi/undefined-sanitizer-on/visibility-hidden/disable-no_post_except_lib.run  
   testing.capture-output bin.v2/libs/contract/test/disable-no_post_except_lib.test/clang-linux-14/release/x86_64/address-sanitizer-norecover/cxxstd-20-iso/link-static/threading-multi/undefined-sanitizer-on/visibility-hidden/disable-no_post_except_lib.run  
```  
  
```  
./b2 libs/contract/test toolset=clang cxxstd=11,17,20 link=shared,static variant=debug,release address-sanitizer=norecover undefined-sanitizer=on  
  
testing.capture-output bin.v2/libs/contract/test/disable-no_post_except_lib.test/clang-linux-14/release/x86_64/address-sanitizer-norecover/cxxstd-20-iso/link-static/threading-multi/undefined-sanitizer-on/visibility-hidden/disable-no_post_except_lib.run  
> ====== BEGIN OUTPUT ======  
f::pre  
f::old  
f::body  
f::post  
x::pre  
> =================================================================  
==467498==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x606000000058 at pc 0x561f4773bf4d bp 0x7ffea0408d10 sp 0x7ffea0408d08  
READ of size 8 at 0x606000000058 thread T0  
    #0 0x561f4773bf4c in boost::contract::detail::cond_base::copy_old() (/home/jking/boost/bin.v2/libs/contract/test/disable-no_post_except_lib.test/clang-linux-14/release/x86_64/address-sanitizer-norecover/cxxstd-20-iso/link-static/threading-multi/undefined-sanitizer-on/visibility-hidden/disable-no_post_except_lib+0xfff4c) (BuildId: 8f2a9e923ff5885af07d2c319f72d582dbe4014d)  
    #1 0x561f47738bb5 in boost::contract::detail::function::init() (/home/jking/boost/bin.v2/libs/contract/test/disable-no_post_except_lib.test/clang-linux-14/release/x86_64/address-sanitizer-norecover/cxxstd-20-iso/link-static/threading-multi/undefined-sanitizer-on/visibility-hidden/disable-no_post_except_lib+0xfcbb5) (BuildId: 8f2a9e923ff5885af07d2c319f72d582dbe4014d)  
    #2 0x561f47736771 in boost::contract::check::check(boost::contract::specify_except const&) (/home/jking/boost/bin.v2/libs/contract/test/disable-no_post_except_lib.test/clang-linux-14/release/x86_64/address-sanitizer-norecover/cxxstd-20-iso/link-static/threading-multi/undefined-sanitizer-on/visibility-hidden/disable-no_post_except_lib+0xfa771) (BuildId: 8f2a9e923ff5885af07d2c319f72d582dbe4014d)  
    #3 0x561f4773f660 in x() (/home/jking/boost/bin.v2/libs/contract/test/disable-no_post_except_lib.test/clang-linux-14/release/x86_64/address-sanitizer-norecover/cxxstd-20-iso/link-static/threading-multi/undefined-sanitizer-on/visibility-hidden/disable-no_post_except_lib+0x103660) (BuildId: 8f2a9e923ff5885af07d2c319f72d582dbe4014d)  
    #4 0x561f4773495e in main (/home/jking/boost/bin.v2/libs/contract/test/disable-no_post_except_lib.test/clang-linux-14/release/x86_64/address-sanitizer-norecover/cxxstd-20-iso/link-static/threading-multi/undefined-sanitizer-on/visibility-hidden/disable-no_post_except_lib+0xf895e) (BuildId: 8f2a9e923ff5885af07d2c319f72d582dbe4014d)  
    #5 0x7f43a4be4249 in __libc_start_call_main csu/../sysdeps/nptl/libc_start_call_main.h:58:16  
    #6 0x7f43a4be4304 in __libc_start_main csu/../csu/libc-start.c:360:3  
    #7 0x561f47673700 in _start (/home/jking/boost/bin.v2/libs/contract/test/disable-no_post_except_lib.test/clang-linux-14/release/x86_64/address-sanitizer-norecover/cxxstd-20-iso/link-static/threading-multi/undefined-sanitizer-on/visibility-hidden/disable-no_post_except_lib+0x37700) (BuildId: 8f2a9e923ff5885af07d2c319f72d582dbe4014d)  
  
0x606000000058 is located 0 bytes to the right of 56-byte region [0x606000000020,0x606000000058)  
allocated by thread T0 here:  
    #0 0x561f4773131d in operator new(unsigned long) (/home/jking/boost/bin.v2/libs/contract/test/disable-no_post_except_lib.test/clang-linux-14/release/x86_64/address-sanitizer-norecover/cxxstd-20-iso/link-static/threading-multi/undefined-sanitizer-on/visibility-hidden/disable-no_post_except_lib+0xf531d) (BuildId: 8f2a9e923ff5885af07d2c319f72d582dbe4014d)  
    #1 0x561f4773f3d0 in x() (/home/jking/boost/bin.v2/libs/contract/test/disable-no_post_except_lib.test/clang-linux-14/release/x86_64/address-sanitizer-norecover/cxxstd-20-iso/link-static/threading-multi/undefined-sanitizer-on/visibility-hidden/disable-no_post_except_lib+0x1033d0) (BuildId: 8f2a9e923ff5885af07d2c319f72d582dbe4014d)  
    #2 0x561f4773495e in main (/home/jking/boost/bin.v2/libs/contract/test/disable-no_post_except_lib.test/clang-linux-14/release/x86_64/address-sanitizer-norecover/cxxstd-20-iso/link-static/threading-multi/undefined-sanitizer-on/visibility-hidden/disable-no_post_except_lib+0xf895e) (BuildId: 8f2a9e923ff5885af07d2c319f72d582dbe4014d)  
    #3 0x7f43a4be4249 in __libc_start_call_main csu/../sysdeps/nptl/libc_start_call_main.h:58:16  
  
SUMMARY: AddressSanitizer: heap-buffer-overflow (/home/jking/boost/bin.v2/libs/contract/test/disable-no_post_except_lib.test/clang-linux-14/release/x86_64/address-sanitizer-norecover/cxxstd-20-iso/link-static/threading-multi/undefined-sanitizer-on/visibility-hidden/disable-no_post_except_lib+0xfff4c) (BuildId: 8f2a9e923ff5885af07d2c319f72d582dbe4014d) in boost::contract::detail::cond_base::copy_old()  
Shadow bytes around the buggy address:  
  0x0c0c7fff7fb0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c0c7fff7fc0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c0c7fff7fd0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c0c7fff7fe0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c0c7fff7ff0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
=>0x0c0c7fff8000: fa fa fa fa 00 00 00 00 00 00 00[fa]fa fa fa fa  
  0x0c0c7fff8010: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x0c0c7fff8020: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x0c0c7fff8030: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x0c0c7fff8040: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
  0x0c0c7fff8050: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
Shadow byte legend (one shadow byte represents 8 application bytes):  
  Addressable:           00  
  Partially addressable: 01 02 03 04 05 06 07   
  Heap left redzone:       fa  
  Freed heap region:       fd  
  Stack left redzone:      f1  
  Stack mid redzone:       f2  
  Stack right redzone:     f3  
  Stack after return:      f5  
  Stack use after scope:   f8  
  Global redzone:          f9  
  Global init order:       f6  
  Poisoned by user:        f7  
  Container overflow:      fc  
  Array cookie:            ac  
  Intra object redzone:    bb  
  ASan internal:           fe  
  Left alloca redzone:     ca  
  Right alloca redzone:    cb  
==467498==ABORTING  
```  
  
```  
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".  
f::pre  
  
Breakpoint 1, boost::contract::detail::cond_base::copy_old (this=0x60e000000040)  
    at ../../../boost/contract/detail/condition/cond_base.hpp:106  
106                 if(failed()) return;  
(gdb) c  
Continuing.  
f::old  
f::body  
f::post  
x::pre  
  
Breakpoint 1, boost::contract::detail::cond_base::copy_old (this=0x60e000000120)  
    at ../../../boost/contract/detail/condition/cond_base.hpp:106  
106                 if(failed()) return;  
(gdb) bt  
#0  boost::contract::detail::cond_base::copy_old (this=0x60e000000120)  
    at ../../../boost/contract/detail/condition/cond_base.hpp:106  
#1  0x00005555556841a8 in boost::contract::detail::function::init (this=0x60e000000120)  
    at ../../../boost/contract/detail/operation/function.hpp:52  
#2  0x000055555568a389 in boost::contract::detail::cond_base::initialize (  
    this=0x60e000000120) at ../../../boost/contract/detail/condition/cond_base.hpp:55  
#3  0x000055555567e37d in boost::contract::check::check (this=0x7fffffffcfa0,   
    contract=...) at ../../../boost/contract/check.hpp:283  
#4  0x000055555569450a in x () at disable/lib_x.cpp:22  
#5  0x00005555556762fb in main () at disable/lib_xy.hpp:49  
(gdb) p old_  
$1 = {<boost::function_n<void>> = {<boost::function_base> = {vtable = 0x0, functor = {  
        members = {obj_ptr = 0xbebebebebebebebe, type = {type = 0xbebebebebebebebe,   
            const_qualified = 190, volatile_qualified = 190},   
          func_ptr = 0xbebebebebebebebe, bound_memfunc_ptr = {  
            memfunc_ptr = (void (boost::detail::function::X::*)(boost::detail::function::X * const, int)) 0xbebebebebebebebe, this adjustment -4702111234474983746,   
            obj_ptr = 0xbebebebebebebebe}, obj_ref = {obj_ptr = 0xbebebebebebebebe,   
            is_const_qualified = 190, is_volatile_qualified = 190}},   
        data = '\276' <repeats 24 times>}}, <boost::detail::function::variadic_function_base<>> = {<No data fields>}, static arity = 0}, <No data fields>}  
  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2025-06-01 16:42:13 UTC  
> Updated at: 2025-06-01 19:02:13 UTC  
> Url: https://github.com/boostorg/contract/issues/36#issuecomment-2927516056  

Using link static, release build, with debug symbols, gcc, and valgrind got slightly more visibility into this:  
  
Link:  
> "g++" -L"/usr/bin" -L"/usr/lib" -Wl,-rpath -Wl,"/usr/bin" -Wl,-rpath -Wl,"/usr/lib" -Wl,-rpath-link -Wl,"/usr/bin" -Wl,-rpath-link -Wl,"/usr/lib" -o "bin.v2/libs/contract/test/disable-no_post_except_lib.test/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/disable-no_post_except_lib" -Wl,--start-group "bin.v2/libs/contract/test/disable-no_post_except_lib.test/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/disable/no_post_except_lib.o" "bin.v2/libs/contract/test/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/libdisable-lib_y.a" "bin.v2/libs/contract/test/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/libdisable-lib_x.a" "bin.v2/libs/contract/build/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/libboost_contract.a" "bin.v2/libs/thread/build/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/libboost_thread.a" "bin.v2/libs/date_time/build/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/libboost_date_time.a" "bin.v2/libs/container/build/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/libboost_container.a" "bin.v2/libs/chrono/build/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/libboost_chrono.a" "bin.v2/libs/atomic/build/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/libboost_atomic.a" "bin.v2/libs/exception/build/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/libboost_exception.a" "bin.v2/libs/iostreams/build/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/libboost_iostreams.a" "bin.v2/libs/regex/build/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/libboost_regex.a" "bin.v2/libs/random/build/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/libboost_random.a" "bin.v2/libs/system/build/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/libboost_system.a"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -licudata -licui18n -licuuc -Wl,--end-group -m64 -pthread -std=c++20 -g -fvisibility=hidden -fvisibility-inlines-hidden   
  
```  
jking@pulsar:~/boost$ ./b2 libs/contract/test toolset=gcc cxxstd=20 link=static variant=release debug-symbols=on  
...  
jking@pulsar:~/boost$ valgrind bin.v2/libs/contract/test/disable-no_post_except_lib.test/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/disable-no_post_except_lib  
==563431== Memcheck, a memory error detector  
==563431== Copyright (C) 2002-2022, and GNU GPL'd, by Julian Seward et al.  
==563431== Using Valgrind-3.19.0 and LibVEX; rerun with -h for copyright info  
==563431== Command: bin.v2/libs/contract/test/disable-no_post_except_lib.test/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/disable-no_post_except_lib  
==563431==   
f::pre  
f::old  
f::body  
f::post  
x::pre  
==563431== Invalid read of size 8  
==563431==    at 0x110198: empty (function_base.hpp:500)  
==563431==    by 0x110198: operator bool (function_template.hpp:888)  
==563431==    by 0x110198: copy_old (cond_base.hpp:107)  
==563431==    by 0x110198: boost::contract::detail::function::init() (function.hpp:52)  
==563431==    by 0x110E26: initialize (cond_base.hpp:55)  
==563431==    by 0x110E26: check (check.hpp:283)  
==563431==    by 0x110E26: x() (lib_x.cpp:25)  
==563431==    by 0x10DA83: main (lib_xy.hpp:49)  
==563431==  Address 0x4d6a1d8 is 0 bytes after a block of size 56 alloc'd  
==563431==    at 0x4841F2F: operator new(unsigned long) (vg_replace_malloc.c:422)  
==563431==    by 0x110DD1: function (function.hpp:81)  
==563431==    by 0x110DD1: x() (lib_x.cpp:22)  
==563431==    by 0x10DA83: main (lib_xy.hpp:49)  
==563431==   
x::body  
==563431== Invalid read of size 8  
==563431==    at 0x110398: empty (function_base.hpp:500)  
==563431==    by 0x110398: operator bool (function_template.hpp:888)  
==563431==    by 0x110398: check_post (cond_post.hpp:75)  
==563431==    by 0x110398: ~function (function.hpp:73)  
==563431==    by 0x110398: boost::contract::detail::function::~function() (function.hpp:76)  
==563431==    by 0x10DA83: main (lib_xy.hpp:49)  
==563431==  Address 0x4d6a218 is 8 bytes before an unallocated block of size 4,120,000 in arena "client"  
==563431==   
==563431== Invalid read of size 8  
==563431==    at 0x1102BE: clear (function_template.hpp:881)  
==563431==    by 0x1102BE: ~function_n (function_template.hpp:782)  
==563431==    by 0x1102BE: ~function (function_template.hpp:1066)  
==563431==    by 0x1102BE: ~cond_post (cond_post.hpp:70)  
==563431==    by 0x1102BE: ~function (function.hpp:76)  
==563431==    by 0x1102BE: boost::contract::detail::function::~function() (function.hpp:76)  
==563431==    by 0x10DA83: main (lib_xy.hpp:49)  
==563431==  Address 0x4d6a218 is 8 bytes before an unallocated block of size 4,120,000 in arena "client"  
==563431==   
==563431== Invalid read of size 8  
==563431==    at 0x1102FC: clear (function_template.hpp:881)  
==563431==    by 0x1102FC: ~function_n (function_template.hpp:782)  
==563431==    by 0x1102FC: ~function (function_template.hpp:1066)  
==563431==    by 0x1102FC: ~cond_base (cond_base.hpp:51)  
==563431==    by 0x1102FC: ~cond_post (cond_post.hpp:70)  
==563431==    by 0x1102FC: ~function (function.hpp:76)  
==563431==    by 0x1102FC: boost::contract::detail::function::~function() (function.hpp:76)  
==563431==    by 0x10DA83: main (lib_xy.hpp:49)  
==563431==  Address 0x4d6a1f8 is 24 bytes after a block of size 64 in arena "client"  
==563431==   
==563431== Invalid read of size 8  
==563431==    at 0x11030D: clear (function_template.hpp:441)  
==563431==    by 0x11030D: clear (function_template.hpp:883)  
==563431==    by 0x11030D: ~function_n (function_template.hpp:782)  
==563431==    by 0x11030D: ~function (function_template.hpp:1066)  
==563431==    by 0x11030D: ~cond_base (cond_base.hpp:51)  
==563431==    by 0x11030D: ~cond_post (cond_post.hpp:70)  
==563431==    by 0x11030D: ~function (function.hpp:76)  
==563431==    by 0x11030D: boost::contract::detail::function::~function() (function.hpp:76)  
==563431==    by 0x10DA83: main (lib_xy.hpp:49)  
==563431==  Address 0x80 is not stack'd, malloc'd or (recently) free'd  
==563431==   
==563431==   
==563431== Process terminating with default action of signal 11 (SIGSEGV)  
==563431==  Access not within mapped region at address 0x80  
==563431==    at 0x11030D: clear (function_template.hpp:441)  
==563431==    by 0x11030D: clear (function_template.hpp:883)  
==563431==    by 0x11030D: ~function_n (function_template.hpp:782)  
==563431==    by 0x11030D: ~function (function_template.hpp:1066)  
==563431==    by 0x11030D: ~cond_base (cond_base.hpp:51)  
==563431==    by 0x11030D: ~cond_post (cond_post.hpp:70)  
==563431==    by 0x11030D: ~function (function.hpp:76)  
==563431==    by 0x11030D: boost::contract::detail::function::~function() (function.hpp:76)  
==563431==    by 0x10DA83: main (lib_xy.hpp:49)  
==563431==  If you believe this happened as a result of a stack  
==563431==  overflow in your program's main thread (unlikely but  
==563431==  possible), you can try to increase the size of the  
==563431==  main thread stack using the --main-stacksize= flag.  
==563431==  The main thread stack size used in this run was 8388608.  
==563431==   
==563431== HEAP SUMMARY:  
==563431==     in use at exit: 73,318 bytes in 4 blocks  
==563431==   total heap usage: 9 allocs, 5 frees, 73,586 bytes allocated  
==563431==   
==563431== LEAK SUMMARY:  
==563431==    definitely lost: 56 bytes in 1 blocks  
==563431==    indirectly lost: 0 bytes in 0 blocks  
==563431==      possibly lost: 0 bytes in 0 blocks  
==563431==    still reachable: 73,262 bytes in 3 blocks  
==563431==         suppressed: 0 bytes in 0 blocks  
==563431== Rerun with --leak-check=full to see details of leaked memory  
==563431==   
==563431== For lists of detected and suppressed errors, rerun with: -s  
==563431== ERROR SUMMARY: 5 errors from 5 contexts (suppressed: 0 from 0)  
Segmentation fault (core dumped)  
```

---

## Comment 2

> Username: jeking3  
> Created at: 2025-06-01 19:10:24 UTC  
> Url: https://github.com/boostorg/contract/issues/36#issuecomment-2927722404  

This is what the f() one looks like in the debugger:  
```  
(gdb) p old_  
$3 = {<boost::function_n<void>> = {<boost::function_base> = {vtable = 0x55555556f371 <boost::function_n<void>::assign_to<f()::{lambda()#2}>(f()::{lambda()#2})::stored_vtable+1>,   
      functor = {members = {obj_ptr = 0x7fffffffd850, type = {type = 0x7fffffffd850, const_qualified = 48, volatile_qualified = 216}, func_ptr = 0x7fffffffd850, bound_memfunc_ptr = {  
            memfunc_ptr = (void (boost::detail::function::X::*)(boost::detail::function::X * const, int)) 0x7fffffffd850, this adjustment 140737488345136, obj_ptr = 0x7ffff7ffe2e0},   
          obj_ref = {obj_ptr = 0x7fffffffd850, is_const_qualified = 48, is_volatile_qualified = 216}},   
        data = "P\330\377\377\377\177\000\0000\330\377\377\377\177\000\000\340\342\377\367\377\177\000"}}, <boost::detail::function::variadic_function_base<>> = {<No data fields>}, <No data fields>}, <No data fields>}  
```  
  
I found that set_old is not being called on the x() one, but it does seem to be intitialized to something:  
```  
(gdb) p old_  
$5 = {<boost::function_n<void>> = {<boost::function_base> = {vtable = 0xedb1, functor = {members = {obj_ptr = 0x0, type = {type = 0x0, const_qualified = false,   
            volatile_qualified = false}, func_ptr = 0x0, bound_memfunc_ptr = {memfunc_ptr = NULL, obj_ptr = 0x0}, obj_ref = {obj_ptr = 0x0, is_const_qualified = false,   
            is_volatile_qualified = false}},   
        data = '\000' <repeats 23 times>}}, <boost::detail::function::variadic_function_base<>> = {<No data fields>}, <No data fields>}, <No data fields>}  
```

---

## Comment 3

> Username: jeking3  
> Created at: 2025-06-01 19:26:56 UTC  
> Updated at: 2025-06-08 16:14:06 UTC  
> Url: https://github.com/boostorg/contract/issues/36#issuecomment-2927743108  

gdb session: set_old is not getting called on the second test case  
```  
(gdb) b boost::contract::detail::cond_base::set_pre  
Breakpoint 1 at 0x55555555a302: boost::contract::detail::cond_base::set_pre. (3 locations)  
(gdb) b boost::contract::detail::cond_base::set_old  
Breakpoint 2 at 0x55555555a337: boost::contract::detail::cond_base::set_old. (2 locations)  
(gdb) r  
The program being debugged has been started already.  
Start it from the beginning? (y or n) y  
Starting program: /home/jking/boost/bin.v2/libs/contract/test/disable-no_post_except_lib.test/gcc-12/release/x86_64/cxxstd-20-iso/debug-symbols-on/link-static/threading-multi/visibility-hidden/disable-no_post_except_lib   
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".  
  
Breakpoint 1.1, boost::contract::detail::cond_base::set_pre<f()::<lambda()> > (f=..., this=<optimized out>) at ./boost/contract/detail/condition/cond_base.hpp:60  
60              void set_pre(F const& f) { pre_ = f; }  
(gdb) c  
Continuing.  
  
Breakpoint 2.1, boost::contract::detail::cond_base::set_old<f()::<lambda()> > (f=..., this=<optimized out>) at ./boost/contract/detail/condition/cond_base.hpp:65  
65              void set_old(F const& f) { old_ = f; }  
(gdb) c  
Continuing.  
f::pre  
f::old  
f::body  
f::post  
  
Breakpoint 1.3, boost::contract::detail::cond_base::set_pre<x()::<lambda()> > (f=..., this=<optimized out>) at ./boost/contract/detail/condition/cond_base.hpp:60  
60              void set_pre(F const& f) { pre_ = f; }  
(gdb) c  
Continuing.  
x::pre  
  
Program received signal SIGSEGV, Segmentation fault.  
0x000055555555c1a9 in boost::function_n<void>::operator() (this=0x555555583258) at ./boost/function/function_template.hpp:789  
789           return get_vtable()->invoker  
(gdb) q  
A debugging session is active.  
  
        Inferior 1 [process 610171] will be killed.  
  
Quit anyway? (y or n) y  
```
