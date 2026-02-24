# #16 - warning: unused parameter 'order' [Closed]

> Username: kivadiu  
> Created at: 2018-07-30 07:24:57 UTC  
> Updated at: 2018-07-30 09:11:21 UTC  
> Closed at: 2018-07-30 09:11:21 UTC  
> Url: https://github.com/boostorg/atomic/issues/16  

This program:  
```  
#include <boost/atomic.hpp>  
int main() {  
  return 0;  
}  
```  
cross-compiled from linux x86_64 to OSX 10.9 with clang++ 5.0.2 with the following command:  
`i386-apple-darwin13-clang++-libc++ -o atomic.o -c -Wall -Wextra -std=c++14 -m32 -mmacosx-version-min=10.9 -I/softs/osx32-clang-5.0.2/release/boost/include atomic.cpp`  
gives the following warning:  
```  
In file included from atomic.cpp:1:  
In file included from /softs/osx32-clang-5.0.2/release/boost/include/boost/atomic.hpp:12:  
In file included from /softs/osx32-clang-5.0.2/release/boost/include/boost/atomic/atomic.hpp:20:  
In file included from /softs/osx32-clang-5.0.2/release/boost/include/boost/atomic/fences.hpp:21:  
In file included from /softs/osx32-clang-5.0.2/release/boost/include/boost/atomic/detail/operations.hpp:17:  
In file included from /softs/osx32-clang-5.0.2/release/boost/include/boost/atomic/detail/operations_lockfree.hpp:21:  
In file included from /softs/osx32-clang-5.0.2/release/boost/include/boost/atomic/detail/ops_gcc_atomic.hpp:24:  
/softs/osx32-clang-5.0.2/release/boost/include/boost/atomic/detail/ops_gcc_x86_dcas.hpp:269:113: warning: unused parameter 'order' [-Wunused-parameter]  
    static BOOST_FORCEINLINE storage_type exchange(storage_type volatile& storage, storage_type v, memory_order order) BOOST_NOEXCEPT  
                                                                                                                ^  
1 warning generated.  
```
