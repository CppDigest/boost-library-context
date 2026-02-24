# #545 - boost-process fails to build on SPARC due to duplicate signals [Closed]

> Username: glaubitz  
> Created at: 2026-02-17 08:37:00 UTC  
> Updated at: 2026-02-18 12:34:44 UTC  
> Closed at: 2026-02-18 12:34:44 UTC  
> Url: https://github.com/boostorg/process/issues/545  

On Linux SPARC, building `boost-process` fails with:  
  
```  
gcc.compile.c++ bin.v2/libs/process/build/gcc-15/release/sparc_64/boost.process.fs-boost/debug-symbols-on/threading-multi/visibility-hidden/error.o  
  
    "g++"   -fvisibility-inlines-hidden -g -O2 -ffile-prefix-map=/build/reproducible-path/boost1.90-1.90.0=. -fstack-protector-strong -Wformat -Werror=format-security -Wno-unused-local-typedefs -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -g -fvisibility=hidden -Wdate-time -D_FORTIFY_SOURCE=2 -mcpu=v7  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_ATOMIC_NO_LIB=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_CONTEXT_NO_LIB=1 -DBOOST_DATE_TIME_DYN_LINK=1 -DBOOST_DISABLE_ASSERTS -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_FILESYSTEM_NO_LIB=1 -DBOOST_PROCESS_DYN_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DDATE_TIME_INLINE -DNDEBUG   -I"." -I"/build/reproducible-path/boost1.90-1.90.0/libs/asio/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/assert/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/atomic/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/concept_check/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/container_hash/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/context/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/core/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/describe/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/detail/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/filesystem/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/integer/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/iterator/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/move/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/mp11/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/mpl/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/optional/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/pool/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/predef/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/preprocessor/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/process/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/scope/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/smart_ptr/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/static_assert/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/system/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/throw_exception/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/tokenizer/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/type_index/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/type_traits/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/utility/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/variant2/include" -I"/build/reproducible-path/boost1.90-1.90.0/libs/winapi/include" -I"libs/config/include"  -c -o "bin.v2/libs/process/build/gcc-15/release/sparc_64/boost.process.fs-boost/debug-symbols-on/threading-multi/visibility-hidden/error.o" "/build/reproducible-path/boost1.90-1.90.0/libs/process/src/error.cpp"  
  
/build/reproducible-path/boost1.90-1.90.0/libs/process/src/error.cpp: In member function ‘virtual std::string boost::process::v2::error::detail::exit_code_category::message(int) const’:  
/build/reproducible-path/boost1.90-1.90.0/libs/process/src/error.cpp:128:21: error: duplicate case value  
  128 |                     case SIGPWR: return "SIGPWR:    Power failure (System V)";  
      |                     ^~~~  
/build/reproducible-path/boost1.90-1.90.0/libs/process/src/error.cpp:116:21: note: previously used here  
  116 |                     case SIGLOST: return "SIGLOST:   File lock lost (unused)";  
      |                     ^~~~  
...failed gcc.compile.c++ bin.v2/libs/process/build/gcc-15/release/sparc_64/boost.process.fs-boost/debug-symbols-on/threading-multi/visibility-hidden/error.o...  
```  
  
Looks similar like https://github.com/boostorg/process/issues/474. Currently testing a patch.  
  
Full build log available [here](https://buildd.debian.org/status/fetch.php?pkg=boost1.90&arch=sparc64&ver=1.90.0-3&stamp=1771026817&raw=0).
