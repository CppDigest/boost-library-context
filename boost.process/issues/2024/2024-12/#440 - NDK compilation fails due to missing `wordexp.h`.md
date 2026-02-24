# #440 - NDK compilation fails due to missing `wordexp.h` [Closed]

> Username: sergiud  
> Created at: 2024-12-17 10:30:50 UTC  
> Updated at: 2024-12-20 02:10:02 UTC  
> Closed at: 2024-12-20 02:10:02 UTC  
> Url: https://github.com/boostorg/process/issues/440  

Trying to build the library using NDK 22b results in  
```console  
<boost-prefix>/src/boost/libs/process/src/shell.cpp:23:10: fatal error: 'wordexp.h' file not found  
#include <wordexp.h>  
         ^~~~~~~~~~~  
1 error generated.  
  
    "ccache" "/devel/android-ndk-r22b/toolchains/llvm/prebuilt/linux-x86_64/bin/clang++"   -fvisibility-inlines-hidden -funwind-tables -no-canonical-prefixes -fexceptions -frtti -stdlib=libc++ --target=x86_64-none-linux-android30 -O3 -Wall -fvisibility=hidden -Wno-inline -m64 -fPIC  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_ATOMIC_NO_LIB=1 -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_CONTEXT_NO_LIB=1 -DBOOST_DISABLE_ASSERTS -DBOOST_FILESYSTEM_NO_LIB=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DBOOST_SYSTEM_NO_LIB=1 -DBOOST_SYSTEM_STATIC_LINK=1 -DDATE_TIME_INLINE -DNDEBUG   -I"." -I"<boost-prefix>/src/boost/libs/asio/include" -I"<boost-prefix>/src/boost/libs/assert/include" -I"<boost-prefix>/src/boost/libs/atomic/include" -I"<boost-prefix>/src/boost/libs/container_hash/include" -I"<boost-prefix>/src/boost/libs/context/include" -I"<boost-prefix>/src/boost/libs/core/include" -I"<boost-prefix>/src/boost/libs/describe/include" -I"<boost-prefix>/src/boost/libs/detail/include" -I"<boost-prefix>/src/boost/libs/filesystem/include" -I"<boost-prefix>/src/boost/libs/function_types/include" -I"<boost-prefix>/src/boost/libs/iterator/include" -I"<boost-prefix>/src/boost/libs/move/include" -I"<boost-prefix>/src/boost/libs/mp11/include" -I"<boost-prefix>/src/boost/libs/optional/include" -I"<boost-prefix>/src/boost/libs/predef/include" -I"<boost-prefix>/src/boost/libs/process/include" -I"<boost-prefix>/src/boost/libs/scope/include" -I"<boost-prefix>/src/boost/libs/smart_ptr/include" -I"<boost-prefix>/src/boost/libs/system/include" -I"<boost-prefix>/src/boost/libs/throw_exception/include" -I"<boost-prefix>/src/boost/libs/type_index/include" -I"<boost-prefix>/src/boost/libs/variant2/include" -I"<boost-prefix>/src/boost/libs/winapi/include"  -c -o "bin.v2/libs/process/build/dcce20e52ac102e835142ce4ad677986/shell.o" "<boost-prefix>/src/boost/libs/process/src/shell.cpp"  
```  
Possibly related to #391.  
  
Boost 1.87.0.
