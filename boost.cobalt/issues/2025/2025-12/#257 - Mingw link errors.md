# #257 - Mingw link errors [Open]

> Username: GrinlexGH  
> Created at: 2025-12-04 10:41:33 UTC  
> Updated at: 2025-12-10 20:45:35 UTC  
> Url: https://github.com/boostorg/cobalt/issues/257  

Compilation with clang and clang64 env cause undefined reference linker errors.  
Need to link not only ws2_32, but also bcrypt and mswsock in CMakeLists.txt on lines 76 and 213.

---

## Comment 1

> Username: akioweh  
> Created at: 2025-12-10 20:45:35 UTC  
> Url: https://github.com/boostorg/cobalt/issues/257#issuecomment-3638895411  

had the exact same issue (from vcpkg install with mingw triple).  
  
I ended up adding the libs to the triple recipe  
```cmake  
list(APPEND VCPKG_CMAKE_CONFIGURE_OPTIONS "-DCMAKE_CXX_STANDARD_LIBRARIES=-lbcrypt -lmswsock")  
```
