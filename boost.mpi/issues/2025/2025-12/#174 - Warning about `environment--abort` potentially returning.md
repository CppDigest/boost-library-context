# #174 - Warning about `environment::abort` potentially returning [Open]

> Username: Lastique  
> Created at: 2025-12-23 20:38:05 UTC  
> Updated at: 2025-12-23 20:38:05 UTC  
> Url: https://github.com/boostorg/mpi/issues/174  

With Boost 1.90.0, I'm seeing this warning:  
  
```  
    "g++"   -std=gnu++23 -g -O2 -fno-omit-frame-pointer -mno-omit-leaf-frame-pointer -ffile-prefix-map=/build/boost/boost=. -flto=auto -ffat-lto-objects -fstack-protector-strong -fstack-clash-protection -Wformat -Werror=format-security -fcf-protection -fdebug-prefix-map=/build/boost/boost=/usr/src/boost-1.90.0 -O3 -fno-math-errno -fno-rounding-math -fno-signaling-nans -fno-trapping-math -ftree-vectorize -fgcse-sm -fgcse-las -fgcse-after-reload -ftree-loop-im -ftree-loop-distribution -ftree-loop-if-convert -funswitch-loops -frename-registers -fivopts -fvisibility-inlines-hidden -fno-plt -faligned-new -fcoroutines -march=haswell -mtune=sapphirerapids -mprefer-avx128 -Wno-unused-local-typedefs -Wno-register -Wno-deprecated-declarations -Wno-unused-local-typedefs -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -g -fvisibility=default -Wdate-time -D_FORTIFY_SOURCE=3  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTAINER_NO_LIB=1 -DBOOST_CONTAINER_STATIC_LINK=1 -DBOOST_MPI_SOURCE=1 -DBOOST_USE_NUMA -DNDEBUG   -I"." -I"/build/boost/boost/libs/assert/include" -I"/build/boost/boost/libs/bind/include" -I"/build/boost/boost/libs/concept_check/include" -I"/build/boost/boost/libs/container/include" -I"/build/boost/boost/libs/core/include" -I"/build/boost/boost/libs/detail/include" -I"/build/boost/boost/libs/function/include" -I"/build/boost/boost/libs/integer/include" -I"/build/boost/boost/libs/intrusive/include" -I"/build/boost/boost/libs/iterator/include" -I"/build/boost/boost/libs/lexical_cast/include" -I"/build/boost/boost/libs/move/include" -I"/build/boost/boost/libs/mpi/include" -I"/build/boost/boost/libs/mpl/include" -I"/build/boost/boost/libs/optional/include" -I"/build/boost/boost/libs/predef/include" -I"/build/boost/boost/libs/preprocessor/include" -I"/build/boost/boost/libs/smart_ptr/include" -I"/build/boost/boost/libs/static_assert/include" -I"/build/boost/boost/libs/throw_exception/include" -I"/build/boost/boost/libs/type_traits/include" -I"/build/boost/boost/libs/utility/include" -I"/usr/lib/x86_64-linux-gnu/openmpi/include" -I"/usr/lib/x86_64-linux-gnu/openmpi/include/openmpi" -I"libs/config/include"  -c -o "build-3.12/boost/bin.v2/libs/mpi/build/gcc-13/release/x86_64/cxxstd-23-gnu/debug-symbols-on/link-static/local-visibility-global/numa-on/threading-multi/visibility-hidden/environment.o" "/build/boost/boost/libs/mpi/src/environment.cpp"  
  
/build/boost/boost/libs/mpi/src/environment.cpp: In static member function 'static void boost::mpi::environment::abort(int)':  
/build/boost/boost/libs/mpi/src/environment.cpp:193:1: warning: 'noreturn' function does return  
  193 | }  
      | ^  
```  
  
The `environment::abort` function calls `MPI_Abort`, which is not marked as noreturn, hence the warning. I'm not familiar with MPI API, so I'm not sure if `MPI_Abort` is intended to never return, but since it has a returned value, I'm assuming it can. So either `environment::abort` should not be marked as noreturn or its implementation should ensure the call doesn't return even if `MPI_Abort` returns (e.g. by calling `std::abort` or throwing an exception).
