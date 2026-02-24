# #57 - `<boost/move/adl_move_swap.hpp>` fails to compile under libc++-19 [Closed]

> Username: pdimov  
> Created at: 2024-11-27 19:49:55 UTC  
> Updated at: 2024-11-27 20:27:16 UTC  
> Closed at: 2024-11-27 20:27:16 UTC  
> Url: https://github.com/boostorg/move/issues/57  

```  
clang-linux.compile.c++ bin.v2/libs/core/test/fclose_deleter_test.test/clang-linux-19/debug/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/fclose_deleter_test.o  
In file included from libs/core/test/fclose_deleter_test.cpp:19:  
In file included from ./boost/move/unique_ptr.hpp:27:  
./boost/move/adl_move_swap.hpp:190:15: error: no member named 'swap' in namespace 'std'  
  190 |    using std::swap;  
      |          ~~~~~^  
1 error generated.  
  
    "clang++-19"   -std=c++11 -fvisibility-inlines-hidden -fPIC -pthread -O0 -fno-inline -Wall -Wextra -Werror -g -fvisibility=hidden -m64 --target=x86_64-pc-linux -stdlib=libc++ -DBOOST_ALL_NO_LIB=1   -I"." -I"/__w/core/core/boost-root/libs/assert/include" -I"/__w/core/core/boost-root/libs/core/include" -I"/__w/core/core/boost-root/libs/move/include" -I"/__w/core/core/boost-root/libs/smart_ptr/include" -I"/__w/core/core/boost-root/libs/throw_exception/include" -I"/__w/core/core/boost-root/libs/type_traits/include"  -c -o "bin.v2/libs/core/test/fclose_deleter_test.test/clang-linux-19/debug/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/fclose_deleter_test.o" "libs/core/test/fclose_deleter_test.cpp"  
  
...failed clang-linux.compile.c++ bin.v2/libs/core/test/fclose_deleter_test.test/clang-linux-19/debug/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/fclose_deleter_test.o...  
```  
(https://github.com/boostorg/core/actions/runs/12056658900/job/33619657708#step:7:5527)
