# #1064 - Boost failed to build on windows using clang [Closed]

> Username: solomoncyj  
> Created at: 2025-07-17 14:34:20 UTC  
> Updated at: 2025-07-17 15:22:03 UTC  
> Closed at: 2025-07-17 15:21:43 UTC  
> Url: https://github.com/boostorg/boost/issues/1064  

```  
 ninja  
[0/2] Re-checking globbed directories...  
[20/254] Building CXX object libs/cobalt/CMakeFiles/boost_cobalt_io.dir/src/io/read.cpp.obj  
FAILED: libs/cobalt/CMakeFiles/boost_cobalt_io.dir/src/io/read.cpp.obj  
C:\PROGRA~1\LLVM\bin\CLANG_~1.EXE -DBOOST_COBALT_IO_SOURCE=1 -DBOOST_COBALT_NO_LIB=1 -DBOOST_COBALT_STATIC_LINK=1 -DBOOST_CONTAINER_NO_LIB -DBOOST_CONTAINER_STATIC_LINK -DBOOST_CONTEXT_EXPORT="" -DBOOST_CONTEXT_NO_LIB="" -DBOOST_CONTEXT_STATIC_LINK="" -DBOOST_DATE_TIME_NO_LIB -DBOOST_DATE_TIME_STATIC_LINK -IC:/Users/solom/Downloads/boost/libs/cobalt/include -IC:/Users/solom/Downloads/boost/libs/asio/include -IC:/Users/solom/Downloads/boost/libs/align/include -IC:/Users/solom/Downloads/boost/libs/assert/include -IC:/Users/solom/Downloads/boost/libs/config/include -IC:/Users/solom/Downloads/boost/libs/core/include -IC:/Users/solom/Downloads/boost/libs/static_assert/include -IC:/Users/solom/Downloads/boost/libs/throw_exception/include -IC:/Users/solom/Downloads/boost/libs/system/include -IC:/Users/solom/Downloads/boost/libs/variant2/include -IC:/Users/solom/Downloads/boost/libs/mp11/include -IC:/Users/solom/Downloads/boost/libs/winapi/include -IC:/Users/solom/Downloads/boost/libs/predef/include -IC:/Users/solom/Downloads/boost/libs/date_time/include -IC:/Users/solom/Downloads/boost/libs/algorithm/include -IC:/Users/solom/Downloads/boost/libs/array/include -IC:/Users/solom/Downloads/boost/libs/bind/include -IC:/Users/solom/Downloads/boost/libs/concept_check/include -IC:/Users/solom/Downloads/boost/libs/preprocessor/include -IC:/Users/solom/Downloads/boost/libs/type_traits/include -IC:/Users/solom/Downloads/boost/libs/exception/include -IC:/Users/solom/Downloads/boost/libs/smart_ptr/include -IC:/Users/solom/Downloads/boost/libs/tuple/include -IC:/Users/solom/Downloads/boost/libs/function/include -IC:/Users/solom/Downloads/boost/libs/iterator/include -IC:/Users/solom/Downloads/boost/libs/detail/include -IC:/Users/solom/Downloads/boost/libs/fusion/include -IC:/Users/solom/Downloads/boost/libs/container_hash/include -IC:/Users/solom/Downloads/boost/libs/describe/include -IC:/Users/solom/Downloads/boost/libs/function_types/include -IC:/Users/solom/Downloads/boost/libs/mpl/include -IC:/Users/solom/Downloads/boost/libs/utility/include -IC:/Users/solom/Downloads/boost/libs/io/include -IC:/Users/solom/Downloads/boost/libs/typeof/include -IC:/Users/solom/Downloads/boost/libs/functional/include -IC:/Users/solom/Downloads/boost/libs/optional/include -IC:/Users/solom/Downloads/boost/libs/range/include -IC:/Users/solom/Downloads/boost/libs/conversion/include -IC:/Users/solom/Downloads/boost/libs/regex/include -IC:/Users/solom/Downloads/boost/libs/unordered/include -IC:/Users/solom/Downloads/boost/libs/lexical_cast/include -IC:/Users/solom/Downloads/boost/libs/container/include -IC:/Users/solom/Downloads/boost/libs/intrusive/include -IC:/Users/solom/Downloads/boost/libs/move/include -IC:/Users/solom/Downloads/boost/libs/numeric/conversion/include -IC:/Users/solom/Downloads/boost/libs/tokenizer/include -IC:/Users/solom/Downloads/boost/libs/context/include -IC:/Users/solom/Downloads/boost/libs/pool/include -IC:/Users/solom/Downloads/boost/libs/integer/include -IC:/Users/solom/Downloads/boost/libs/callable_traits/include -IC:/Users/solom/Downloads/boost/libs/circular_buffer/include -IC:/Users/solom/Downloads/boost/libs/endian/include -IC:/Users/solom/Downloads/boost/libs/static_string/include -march=native -O3 -DNDEBUG -std=gnu++20 -D_DLL -D_MT -Xclang --dependent-lib=msvcrt -fvisibility-inlines-hidden -MD -MT libs/cobalt/CMakeFiles/boost_cobalt_io.dir/src/io/read.cpp.obj -MF libs\cobalt\CMakeFiles\boost_cobalt_io.dir\src\io\read.cpp.obj.d -o libs/cobalt/CMakeFiles/boost_cobalt_io.dir/src/io/read.cpp.obj -c C:/Users/solom/Downloads/boost/libs/cobalt/src/io/read.cpp  
In file included from C:/Users/solom/Downloads/boost/libs/cobalt/src/io/read.cpp:8:  
In file included from C:/Users/solom/Downloads/boost/libs/cobalt/include\boost/cobalt/io/read.hpp:11:  
In file included from C:/Users/solom/Downloads/boost/libs/cobalt/include\boost/cobalt/io/buffer.hpp:11:  
In file included from C:/Users/solom/Downloads/boost/libs/asio/include\boost/asio/buffer.hpp:18:  
C:/Users/solom/Downloads/boost/libs/asio/include\boost/asio/detail/config.hpp:717:12: warning: Please define _WIN32_WINNT or _WIN32_WINDOWS appropriately. For example:  
- add -D_WIN32_WINNT=0x0601 to the compiler command line; or  
- add _WIN32_WINNT=0x0601 to your project's Preprocessor Definitions.  
Assuming _WIN32_WINNT=0x0601 (i.e. Windows 7 target). [-W#pragma-messages]  
  717 | #   pragma message( \  
      |            ^  
In file included from C:/Users/solom/Downloads/boost/libs/cobalt/src/io/read.cpp:8:  
C:/Users/solom/Downloads/boost/libs/cobalt/include\boost/cobalt/io/read.hpp:34:3: error: 'nodiscard' attribute cannot be applied to types  
   34 | [[nodiscard]] read_all read(Stream & str, mutable_buffer_sequence buffer)  
      |   ^  
C:/Users/solom/Downloads/boost/libs/cobalt/include\boost/cobalt/io/read.hpp:55:3: error: 'nodiscard' attribute cannot be applied to types  
   55 | [[nodiscard]] read_all_at read_at(Stream & str, std::uint64_t offset, mutable_buffer_sequence buffer)  
      |   ^  
1 warning and 2 errors generated.  
[37/254] Building CXX object libs/graph/CMakeFiles/boost_graph.dir/src/read_graphviz_new.cpp.obj  
ninja: build stopped: subcommand failed.  
```

---

## Comment 1

> Username: solomoncyj  
> Created at: 2025-07-17 15:22:02 UTC  
> Url: https://github.com/boostorg/boost/issues/1064#issuecomment-3084487685  

https://github.com/boostorg/cobalt/issues/237 use this instead
