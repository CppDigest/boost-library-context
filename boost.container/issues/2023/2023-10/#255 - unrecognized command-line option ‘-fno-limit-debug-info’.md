# #255 - unrecognized command-line option ‘-fno-limit-debug-info’ [Closed]

> Username: DNKpp  
> Created at: 2023-10-07 23:18:15 UTC  
> Updated at: 2024-09-30 21:55:25 UTC  
> Closed at: 2024-09-30 21:55:24 UTC  
> Url: https://github.com/boostorg/container/issues/255  

boost version: 1.83.0  
  
On my ubuntu runner with clang 14.0.0, the following error appears during compilation.  
```  
FAILED: _deps/boost-build/libs/container/CMakeFiles/boost_container.dir/src/alloc_lib.c.o   
/usr/bin/cc -DBOOST_CONTAINER_NO_LIB -DBOOST_CONTAINER_STATIC_LINK -I/home/dnkpp/.vs/Simple-Utility/0fe7e91d-e50a-4d70-95e2-57ba0e29f76b/out/build/Linux-Clang-Debug/_deps/boost-src/libs/container/include -I/home/dnkpp/.vs/Simple-Utility/0fe7e91d-e50a-4d70-95e2-57ba0e29f76b/out/build/Linux-Clang-Debug/_deps/boost-src/libs/assert/include -I/home/dnkpp/.vs/Simple-Utility/0fe7e91d-e50a-4d70-95e2-57ba0e29f76b/out/build/Linux-Clang-Debug/_deps/boost-src/libs/config/include -I/home/dnkpp/.vs/Simple-Utility/0fe7e91d-e50a-4d70-95e2-57ba0e29f76b/out/build/Linux-Clang-Debug/_deps/boost-src/libs/intrusive/include -I/home/dnkpp/.vs/Simple-Utility/0fe7e91d-e50a-4d70-95e2-57ba0e29f76b/out/build/Linux-Clang-Debug/_deps/boost-src/libs/container_hash/include -I/home/dnkpp/.vs/Simple-Utility/0fe7e91d-e50a-4d70-95e2-57ba0e29f76b/out/build/Linux-Clang-Debug/_deps/boost-src/libs/describe/include -I/home/dnkpp/.vs/Simple-Utility/0fe7e91d-e50a-4d70-95e2-57ba0e29f76b/out/build/Linux-Clang-Debug/_deps/boost-src/libs/mp11/include -I/home/dnkpp/.vs/Simple-Utility/0fe7e91d-e50a-4d70-95e2-57ba0e29f76b/out/build/Linux-Clang-Debug/_deps/boost-src/libs/type_traits/include -I/home/dnkpp/.vs/Simple-Utility/0fe7e91d-e50a-4d70-95e2-57ba0e29f76b/out/build/Linux-Clang-Debug/_deps/boost-src/libs/static_assert/include -I/home/dnkpp/.vs/Simple-Utility/0fe7e91d-e50a-4d70-95e2-57ba0e29f76b/out/build/Linux-Clang-Debug/_deps/boost-src/libs/move/include -fno-limit-debug-info -g -MD -MT _deps/boost-build/libs/container/CMakeFiles/boost_container.dir/src/alloc_lib.c.o -MF _deps/boost-build/libs/container/CMakeFiles/boost_container.dir/src/alloc_lib.c.o.d -o _deps/boost-build/libs/container/CMakeFiles/boost_container.dir/src/alloc_lib.c.o -c /home/dnkpp/.vs/Simple-Utility/0fe7e91d-e50a-4d70-95e2-57ba0e29f76b/out/build/Linux-Clang-Debug/_deps/boost-src/libs/container/src/alloc_lib.c  
cc: error: unrecognized command-line option ‘-fno-limit-debug-info’  
```  
  
Actually I'm using ``boost::graph`` via cmake, but this seems to be a necessary dependency.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-04-16 13:58:03 UTC  
> Url: https://github.com/boostorg/container/issues/255#issuecomment-2059164713  

This does not seem a Boost.Container issue as the compiler commands and flags are not produced by this library.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-09-30 21:55:24 UTC  
> Url: https://github.com/boostorg/container/issues/255#issuecomment-2384235088  

Closing  the bug as "not a defect"
