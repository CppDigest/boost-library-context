# #82 - ASan container-overflow in vectorstream_test [Closed]

> Username: Kojoley  
> Created at: 2019-03-23 18:01:35 UTC  
> Updated at: 2021-02-19 21:26:57 UTC  
> Closed at: 2021-02-19 21:26:57 UTC  
> Url: https://github.com/boostorg/interprocess/issues/82  

To reproduce you need an instrumented stdlib (I used clang-7 and libc++-7 from LLVM repo).  
  
```  
==19941==ERROR: AddressSanitizer: container-overflow on address 0x62600000360a at pc 0x00000043496a bp 0x7ffc8aded210 sp 0x7ffc8adec998  
READ of size 1291 at 0x62600000360a thread T0  
    #0 0x434969 in __interceptor_strcmp /build/llvm-toolchain-7-7.0.1~svn348686/projects/compiler-rt/lib/asan/../sanitizer_common/sanitizer_common_interceptors.inc:432:3  
    #1 0x500317 in vectorstream_test() /home/debian/boost-root/libs/interprocess/test/vectorstream_test.cpp:108:10  
    #2 0x4fa2d3 in main /home/debian/boost-root/libs/interprocess/test/vectorstream_test.cpp:179:7  
    #3 0x7fc25ace62e0 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x202e0)  
    #4 0x420319 in _start (/home/debian/boost-root/bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi/visibility-hidden/vectorstream_test+0x420319)  
  
0x62600000360a is located 1290 bytes inside of 10000-byte region [0x626000003100,0x626000005810)  
allocated by thread T0 here:  
    #0 0x4f73b2 in operator new(unsigned long) /build/llvm-toolchain-7-7.0.1~svn348686/projects/compiler-rt/lib/asan/asan_new_delete.cc:106:3  
    #1 0x5264d8 in std::__1::__libcpp_allocate(unsigned long, unsigned long) /usr/lib/llvm-7/bin/../include/c++/v1/new:252:10  
    #2 0x5264d8 in std::__1::allocator<char>::allocate(unsigned long, void const*) /usr/lib/llvm-7/bin/../include/c++/v1/memory:1799  
    #3 0x5264d8 in std::__1::allocator_traits<std::__1::allocator<char> >::allocate(std::__1::allocator<char>&, unsigned long) /usr/lib/llvm-7/bin/../include/c++/v1/memory:1548  
    #4 0x5264d8 in std::__1::__split_buffer<char, std::__1::allocator<char>&>::__split_buffer(unsigned long, unsigned long, std::__1::allocator<char>&) /usr/lib/llvm-7/bin/../include/c++/v1/__split_buffer:311  
    #5 0x531ac9 in std::__1::vector<char, std::__1::allocator<char> >::reserve(unsigned long) /usr/lib/llvm-7/bin/../include/c++/v1/vector:1574:53  
    #6 0x50b6d7 in boost::interprocess::basic_vectorbuf<std::__1::vector<char, std::__1::allocator<char> >, std::__1::char_traits<char> >::reserve(unsigned long) /home/debian/boost-root/./boost/interprocess/streams/vectorstream.hpp:158:17  
    #7 0x50b464 in boost::interprocess::basic_vectorstream<std::__1::vector<char, std::__1::allocator<char> >, std::__1::char_traits<char> >::reserve(unsigned long) /home/debian/boost-root/./boost/interprocess/streams/vectorstream.hpp:598:17  
    #8 0x4ff975 in vectorstream_test() /home/debian/boost-root/libs/interprocess/test/vectorstream_test.cpp:101:23  
    #9 0x4fa2d3 in main /home/debian/boost-root/libs/interprocess/test/vectorstream_test.cpp:179:7  
    #10 0x7fc25ace62e0 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x202e0)  
  
HINT: if you don't care about these errors you may set ASAN_OPTIONS=detect_container_overflow=0.  
If you suspect a false positive see also: https://github.com/google/sanitizers/wiki/AddressSanitizerContainerOverflow.  
SUMMARY: AddressSanitizer: container-overflow /build/llvm-toolchain-7-7.0.1~svn348686/projects/compiler-rt/lib/asan/../sanitizer_common/sanitizer_common_interceptors.inc:432:3 in __interceptor_strcmp  
Shadow bytes around the buggy address:  
  0x0c4c7fff8670: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c4c7fff8680: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c4c7fff8690: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c4c7fff86a0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c4c7fff86b0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
=>0x0c4c7fff86c0: 00[02]fc fc fc fc fc fc fc fc fc fc fc fc fc fc  
  0x0c4c7fff86d0: fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc  
  0x0c4c7fff86e0: fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc  
  0x0c4c7fff86f0: fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc  
  0x0c4c7fff8700: fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc  
  0x0c4c7fff8710: fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc  
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
  Shadow gap:              cc  
==19941==ABORTING  
```  
  
<details><summary>Full log</summary>  
  
```  
$ ./b2 toolset=clang-7 sanitize=address variant=debug libs/interprocess/test//vectorstream_test stdlib=libc++ -d+2 2>&1 | tee vectorstream_test.log  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
common.mkdir bin.v2/libs  
  
        mkdir -p "bin.v2/libs"  
      
common.mkdir bin.v2/libs/interprocess  
  
        mkdir -p "bin.v2/libs/interprocess"  
      
common.mkdir bin.v2/libs/interprocess/test  
  
        mkdir -p "bin.v2/libs/interprocess/test"  
      
common.mkdir bin.v2/libs/interprocess/test/vectorstream_test.test  
  
        mkdir -p "bin.v2/libs/interprocess/test/vectorstream_test.test"  
      
common.mkdir bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7  
  
        mkdir -p "bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7"  
      
common.mkdir bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug  
  
        mkdir -p "bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug"  
      
common.mkdir bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address  
  
        mkdir -p "bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address"  
      
common.mkdir bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++  
  
        mkdir -p "bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++"  
      
common.mkdir bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi  
  
        mkdir -p "bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi"  
      
common.mkdir bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi/visibility-hidden  
  
        mkdir -p "bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi/visibility-hidden"  
      
clang-linux.compile.c++.without-pch bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi/visibility-hidden/vectorstream_test.o  
  
  "clang++-7" -c -x c++ -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -fsanitize=address -stdlib=libc++ -DBOOST_ALL_NO_LIB=1 -I"." -o "bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi/visibility-hidden/vectorstream_test.o" "libs/interprocess/test/vectorstream_test.cpp"  
  
clang-linux.link bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi/visibility-hidden/vectorstream_test  
  
    "clang++-7"    -o "bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi/visibility-hidden/vectorstream_test" -Wl,--start-group "bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi/visibility-hidden/vectorstream_test.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -m64 -pthread -g -fvisibility=hidden -fvisibility-inlines-hidden -fsanitize=address -lrt -stdlib=libc++  
  
testing.capture-output bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi/visibility-hidden/vectorstream_test.run  
  
      
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi/visibility-hidden/vectorstream_test"   > "bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi/visibility-hidden/vectorstream_test.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi/visibility-hidden/vectorstream_test.output"  
    echo EXIT STATUS: $status >> "bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi/visibility-hidden/vectorstream_test.output"  
    if test $status -eq 0 ; then  
        cp "bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi/visibility-hidden/vectorstream_test.output" "bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi/visibility-hidden/vectorstream_test.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi/visibility-hidden/vectorstream_test.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
====== BEGIN OUTPUT ======  
=================================================================  
==19941==ERROR: AddressSanitizer: container-overflow on address 0x62600000360a at pc 0x00000043496a bp 0x7ffc8aded210 sp 0x7ffc8adec998  
READ of size 1291 at 0x62600000360a thread T0  
    #0 0x434969 in __interceptor_strcmp /build/llvm-toolchain-7-7.0.1~svn348686/projects/compiler-rt/lib/asan/../sanitizer_common/sanitizer_common_interceptors.inc:432:3  
    #1 0x500317 in vectorstream_test() /home/debian/boost-root/libs/interprocess/test/vectorstream_test.cpp:108:10  
    #2 0x4fa2d3 in main /home/debian/boost-root/libs/interprocess/test/vectorstream_test.cpp:179:7  
    #3 0x7fc25ace62e0 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x202e0)  
    #4 0x420319 in _start (/home/debian/boost-root/bin.v2/libs/interprocess/test/vectorstream_test.test/clang-linux-7/debug/sanitize-address/stdlib-libc++/threading-multi/visibility-hidden/vectorstream_test+0x420319)  
  
0x62600000360a is located 1290 bytes inside of 10000-byte region [0x626000003100,0x626000005810)  
allocated by thread T0 here:  
    #0 0x4f73b2 in operator new(unsigned long) /build/llvm-toolchain-7-7.0.1~svn348686/projects/compiler-rt/lib/asan/asan_new_delete.cc:106:3  
    #1 0x5264d8 in std::__1::__libcpp_allocate(unsigned long, unsigned long) /usr/lib/llvm-7/bin/../include/c++/v1/new:252:10  
    #2 0x5264d8 in std::__1::allocator<char>::allocate(unsigned long, void const*) /usr/lib/llvm-7/bin/../include/c++/v1/memory:1799  
    #3 0x5264d8 in std::__1::allocator_traits<std::__1::allocator<char> >::allocate(std::__1::allocator<char>&, unsigned long) /usr/lib/llvm-7/bin/../include/c++/v1/memory:1548  
    #4 0x5264d8 in std::__1::__split_buffer<char, std::__1::allocator<char>&>::__split_buffer(unsigned long, unsigned long, std::__1::allocator<char>&) /usr/lib/llvm-7/bin/../include/c++/v1/__split_buffer:311  
    #5 0x531ac9 in std::__1::vector<char, std::__1::allocator<char> >::reserve(unsigned long) /usr/lib/llvm-7/bin/../include/c++/v1/vector:1574:53  
    #6 0x50b6d7 in boost::interprocess::basic_vectorbuf<std::__1::vector<char, std::__1::allocator<char> >, std::__1::char_traits<char> >::reserve(unsigned long) /home/debian/boost-root/./boost/interprocess/streams/vectorstream.hpp:158:17  
    #7 0x50b464 in boost::interprocess::basic_vectorstream<std::__1::vector<char, std::__1::allocator<char> >, std::__1::char_traits<char> >::reserve(unsigned long) /home/debian/boost-root/./boost/interprocess/streams/vectorstream.hpp:598:17  
    #8 0x4ff975 in vectorstream_test() /home/debian/boost-root/libs/interprocess/test/vectorstream_test.cpp:101:23  
    #9 0x4fa2d3 in main /home/debian/boost-root/libs/interprocess/test/vectorstream_test.cpp:179:7  
    #10 0x7fc25ace62e0 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x202e0)  
  
HINT: if you don't care about these errors you may set ASAN_OPTIONS=detect_container_overflow=0.  
If you suspect a false positive see also: https://github.com/google/sanitizers/wiki/AddressSanitizerContainerOverflow.  
SUMMARY: AddressSanitizer: container-overflow /build/llvm-toolchain-7-7.0.1~svn348686/projects/compiler-rt/lib/asan/../sanitizer_common/sanitizer_common_interceptors.inc:432:3 in __interceptor_strcmp  
Shadow bytes around the buggy address:  
  0x0c4c7fff8670: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c4c7fff8680: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c4c7fff8690: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c4c7fff86a0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c4c7fff86b0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
=>0x0c4c7fff86c0: 00[02]fc fc fc fc fc fc fc fc fc fc fc fc fc fc  
  0x0c4c7fff86d0: fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc  
  0x0c4c7fff86e0: fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc  
  0x0c4c7fff86f0: fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc  
  0x0c4c7fff8700: fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc  
  0x0c4c7fff8710: fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc fc  
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
  Shadow gap:              cc  
==19941==ABORTING  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
...failed updating 1 target...  
```  
  
</details>

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-02-19 21:26:57 UTC  
> Url: https://github.com/boostorg/interprocess/issues/82#issuecomment-782365063  

Thanks for the report! This was fixed in commit:  
  
https://github.com/boostorg/interprocess/commit/26be575e666ba0de46557d25b6b48cff6ffcbba7
