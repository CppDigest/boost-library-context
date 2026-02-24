# #603 - undefined reference to `bool boost::math::isnan<long double>(long double)' [Closed]

> Username: jschueller  
> Created at: 2021-04-07 17:31:08 UTC  
> Updated at: 2021-04-07 17:45:49 UTC  
> Closed at: 2021-04-07 17:45:48 UTC  
> Url: https://github.com/boostorg/math/issues/603  

when cross-compiling from mingw (x86_64-w64-mingw32), I get a compile error with 1.76b1:  
```  
"x86_64-w64-mingw32-g++"  "-Wl,--out-implib,bin.v2/libs/math/build/gcc-mingw64/release/pch-off/target-os-windows/threading-multi/visibility-hidden/libboost_math_c99.dll.a" -o "bin.v2/libs/math/build/gcc-mingw64/release/pch-off/target-os-windows/threading-multi/visibility-hidden/libboost_math_c99.dll" -shared @"bin.v2/libs/math/build/gcc-mingw64/release/pch-off/target-os-windows/threading-multi/visibility-hidden/libboost_math_c99.dll.rsp" -m64 -mthreads -fvisibility=hidden -fvisibility-inlines-hidden -Wl,-O1,--sort-common,--as-needed -fstack-protector  
  
/usr/lib/gcc/x86_64-w64-mingw32/10.2.0/../../../../x86_64-w64-mingw32/bin/ld: bin.v2/libs/math/build/gcc-mingw64/release/pch-off/target-os-windows/threading-multi/visibility-hidden/acosh.o:acosh.cpp:(.text+0x281): undefined reference to `bool boost::math::isnan<long double>(long double)'  
/usr/lib/gcc/x86_64-w64-mingw32/10.2.0/../../../../x86_64-w64-mingw32/bin/ld: bin.v2/libs/math/build/gcc-mingw64/release/pch-off/target-os-windows/threading-multi/visibility-hidden/asinh.o:asinh.cpp:(.text+0x343): undefined reference to `bool boost::math::isnan<long double>(long double)'  
/usr/lib/gcc/x86_64-w64-mingw32/10.2.0/../../../../x86_64-w64-mingw32/bin/ld: bin.v2/libs/math/build/gcc-mingw64/release/pch-off/target-os-windows/threading-multi/visibility-hidden/atanh.o:atanh.cpp:(.text+0x2c5): undefined reference to `bool boost::math::isnan<long double>(long double)'  
collect2: error: ld returned 1 exit status  
...failed gcc.link.dll.mingw bin.v2/libs/math/build/gcc-mingw64/release/pch-off/target-os-windows/threading-multi/visibility-hidden/libboost_math_c99.dll.a bin.v2/libs/math/build/gcc-mingw64/release/pch-off/target-os-windows/threading-multi/visibility-hidden/libboost_math_c99.dll...  
...removing bin.v2/libs/math/build/gcc-mingw64/release/pch-off/target-os-windows/threading-multi/visibility-hidden/libboost_math_c99.dll.a  
...skipped <p/tmp/boo/pkg/mingw-w64-boost/usr/x86_64-w64-mingw32/lib>libboost_math_c99.dll.a for lack of <pbin.v2/libs/math/build/gcc-mingw64/release/pch-off/target-os-windows/threading-multi/visibility-hidden>libboost_math_c99.dll.a...  
gcc.compile.c++ bin.v2/libs/math/build/gcc-mingw64/release/pch-off/target-os-windows/threading-multi/visibility-hidden/copysignf.o  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-04-07 17:45:48 UTC  
> Url: https://github.com/boostorg/math/issues/603#issuecomment-815102832  

This should have been fixed post-beta in https://github.com/boostorg/math/commit/89216def5d0901cf4a3b8dd992fdbbf65fda63f9
