# #188 - `type_id.cpp` incorrectly includes `cxxabi.h` even when compiling under libc++abi [Open]

> Username: gnaggnoyil  
> Created at: 2018-02-25 20:36:17 UTC  
> Updated at: 2018-02-25 20:36:17 UTC  
> Url: https://github.com/boostorg/python/issues/188  

Clang itself defines `__GNUC__` and in case of Clang 5, `__GNUC__` is defined to 4. So when compiling `src/converter/type_id.cpp` using clang 5 and its corresponding libc++ and libc++abi, the src file is processed to included `cxxabi.h` after some detections using macro. However, libc++abi itself [does not install its headers](https://github.com/llvm-mirror/libcxxabi/commit/c20d83676d5f48624bd57c5bd70201455ddc30c8) so there is no `cxxabi.h` in system header include directories. Therefore a compile error occurs:  
  
```shell  
clang-linux.compile.c++.without-pth bin.v2/libs/python/build/clang-gnu-linux-5.0.1/release/threadapi-pthread/threading-multi/converter/type_id.o  
libs/python/src/converter/type_id.cpp:35:14: fatal error: 'cxxabi.h' file not found  
#    include <cxxabi.h>  
             ^~~~~~~~~~  
1 error generated.  
  
  "clang++" -c -x c++ -std=c++14 -stdlib=libc++ -O3 -Wno-inline -Wall -pthread -fPIC -m64 -std=c++14 -stdlib=libc++ -DBOOST_ALL_NO_LIB=1 -DBOOST_PYTHON_SOURCE -DNDEBUG -I"." -I"/usr/include/python3.6m" -o "bin.v2/libs/python/build/clang-gnu-linux-5.0.1/release/threadapi-pthread/threading-multi/converter/type_id.o" "libs/python/src/converter/type_id.cpp"  
  
...failed clang-linux.compile.c++.without-pth bin.v2/libs/python/build/clang-gnu-linux-5.0.1/release/threadapi-pthread/threading-multi/converter/type_id.o...  
```
