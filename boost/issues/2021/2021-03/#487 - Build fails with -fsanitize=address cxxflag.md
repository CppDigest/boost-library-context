# #487 - Build fails with -fsanitize=address cxxflag [Closed]

> Username: hutm  
> Created at: 2021-03-03 17:38:39 UTC  
> Updated at: 2021-03-03 22:26:38 UTC  
> Closed at: 2021-03-03 22:26:37 UTC  
> Url: https://github.com/boostorg/boost/issues/487  

Building boost 1.75.0 fails with the following cxxflags:  
`-fsanitize=address -fno-optimize-sibling-calls -fsanitize-address-use-after-scope -fno-omit-frame-pointer -g -O0`  
Upgrading form 1.68.0, where the problem was not observed.   
Build log (last chunk):  
```  
boost-install.generate-cmake-variant- bin.v2/libs/serialization/build/gcc-8.3.0/release/link-static/threading-multi/visibility-hidden/libboost_wserialization-variant-static.cmake  
common.copy /build/boost_1_75_0/stage/lib/cmake/boost_wserialization-1.75.0/libboost_wserialization-variant-static.cmake  
common.copy /build/boost_1_75_0/stage/lib/libboost_wserialization.a  
gcc.compile.c++ bin.v2/libs/wave/build/gcc-8.3.0/release/link-static/threading-multi/visibility-hidden/instantiate_re2c_lexer.o  
gcc.compile.c++ bin.v2/libs/wave/build/gcc-8.3.0/release/link-static/threading-multi/visibility-hidden/instantiate_cpp_exprgrammar.o  
gcc.compile.c++ bin.v2/libs/wave/build/gcc-8.3.0/release/link-static/threading-multi/visibility-hidden/instantiate_re2c_lexer_str.o  
gcc.archive bin.v2/libs/wave/build/gcc-8.3.0/release/link-static/threadapi-pthread/threading-multi/visibility-hidden/libboost_wave.a  
boost-install.generate-cmake-variant- bin.v2/libs/wave/build/gcc-8.3.0/release/link-static/threadapi-pthread/threading-multi/visibility-hidden/libboost_wave-variant-static.cmake  
common.copy /build/boost_1_75_0/stage/lib/cmake/boost_wave-1.75.0/libboost_wave-variant-static.cmake  
common.copy /build/boost_1_75_0/stage/lib/libboost_wave.a  
...updated 1789 targets...  
  
  
The Boost C++ Libraries were successfully built!  
  
The following directory should be added to compiler include paths:  
  
    /build/boost_1_75_0  
  
The following directory should be added to linker library paths:  
  
    /build/boost_1_75_0/stage/lib  
  
  
=================================================================  
==226==ERROR: LeakSanitizer: detected memory leaks  
  
Direct leak of 23 byte(s) in 1 object(s) allocated from:  
    #0 0x7ffff72f3350 in malloc (/nix/store/784rh7jrfhagbkydjfrv68h9x3g4gqmk-gcc-8.3.0-lib/lib/libasan.so.5+0xec350)  
    #1 0x7ffff72bdd7d  (/nix/store/784rh7jrfhagbkydjfrv68h9x3g4gqmk-gcc-8.3.0-lib/lib/libasan.so.5+0xb6d7d)  
    #2 0x45261e  (/build/boost_1_75_0/b2+0x45261e)  
    #3 0x491a63  (/build/boost_1_75_0/b2+0x491a63)  
    #4 0x452317  (/build/boost_1_75_0/b2+0x452317)  
    #5 0x7ffff6018b8d in __libc_start_main (/nix/store/wx1vk75bpdr65g6xwxbj4rw0pk04v5j3-glibc-2.27/lib/libc.so.6+0x22b8d)  
  
SUMMARY: AddressSanitizer: 23 byte(s) leaked in 1 allocation(s).  
builder for '/nix/store/h94kwgndvhvin7m8wcw18pfrvj4ggf7d-boost_1_75_0-dbg-ubsan.drv' failed with exit code 1  
error: build of '/nix/store/h94kwgndvhvin7m8wcw18pfrvj4ggf7d-boost_1_75_0-dbg-ubsan.drv' failed  
WARNING! library boost not built  
```

---

## Comment 1

> Username: hutm  
> Created at: 2021-03-03 22:26:37 UTC  
> Url: https://github.com/boostorg/boost/issues/487#issuecomment-790117424  

fixed in https://github.com/boostorg/build/pull/669
