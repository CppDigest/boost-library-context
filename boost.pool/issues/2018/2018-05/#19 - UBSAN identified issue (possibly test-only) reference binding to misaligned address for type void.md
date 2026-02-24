# #19 - UBSAN identified issue (possibly test-only) reference binding to misaligned address for type void * [Closed]

> Username: jeking3  
> Created at: 2018-05-13 22:13:36 UTC  
> Updated at: 2018-10-30 12:07:12 UTC  
> Closed at: 2018-10-30 12:07:12 UTC  
> Url: https://github.com/boostorg/pool/issues/19  

To repeat, using the docker build image in https://github.com/boostorg/boost/pull/184:  
  
```  
boost@b0876a0006ee:/boost/libs/pool$ UBSAN_OPTIONS=print_stacktrace=1 ../../b2 cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=undefined cxxflags=-fno-sanitize-recover=undefined linkflags=-fsanitize=undefined linkflags=-fno-sanitize-recover=undefined linkflags=-fuse-ld=gold variant=debug cxxstd=03 -q  
```  
  
Results:  
```  
gcc.link ../../bin.v2/libs/pool/test/test_simple_seg_storage.test/gcc-7.3/debug/cxxstd-03-iso/test_simple_seg_storage  
testing.capture-output ../../bin.v2/libs/pool/test/test_simple_seg_storage.test/gcc-7.3/debug/cxxstd-03-iso/test_simple_seg_storage.run  
====== BEGIN OUTPUT ======  
../../boost/pool/simple_segregated_storage.hpp:91:41: runtime error: reference binding to misaligned address 0x56448cd91361 for type 'void *', which requires 8 byte alignment  
0x56448cd91361: note: pointer points here  
 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  31 00 00 00 00  
              ^  
    #0 0x56448cb511e6 in boost::simple_segregated_storage<unsigned long>::nextof(void*) ../../boost/pool/simple_segregated_storage.hpp:91  
    #1 0x56448cb52b69 in boost::simple_segregated_storage<unsigned long>::segregate(void*, unsigned long, unsigned long, void*) ../../boost/pool/simple_segregated_storage.hpp:294  
    #2 0x56448cb51519 in boost::simple_segregated_storage<unsigned long>::add_block(void*, unsigned long, unsigned long) ../../boost/pool/simple_segregated_storage.hpp:116  
    #3 0x56448cb4fcdd in test_simp_seg_store::add_block(void*, unsigned long, unsigned long) test/test_simple_seg_storage.hpp:103  
    #4 0x56448cb4d6f4 in main test/test_simple_seg_storage.cpp:163  
    #5 0x7f1e6dc80b96 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21b96)  
    #6 0x56448cb4cc79 in _start (/boost/bin.v2/libs/pool/test/test_simple_seg_storage.test/gcc-7.3/debug/cxxstd-03-iso/test_simple_seg_storage+0xcc79)  
  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```

---

## Comment 1

> Username: mclow  
> Created at: 2018-05-15 00:20:05 UTC  
> Url: https://github.com/boostorg/pool/issues/19#issuecomment-389003009  

I cannot reproduce this on Mac OS w/o the docker container. (using either apple's clang, or a clang I built myself).  
  
[Not saying it doesn't happen; just that I can't make it happen with my setup]

---

## Comment 2

> Username: jeking3  
> Created at: 2018-08-01 13:19:48 UTC  
> Url: https://github.com/boostorg/pool/issues/19#issuecomment-409571780  

Probably due to the differences in UBSAN capabilities when used with gcc vs. clang.  The docker container is using gcc 7.3.0.  Saw similar discrepancies in other discussions recently where one caught it but another did not.  I would still treat it as an issue that needs resolution.
