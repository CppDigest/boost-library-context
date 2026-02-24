# #148 Add missing `<exception>` include. [Open]

> Username: kalaxy  
> Created at: 2025-12-16 01:12:40 UTC  
> Updated at: 2025-12-16 01:12:40 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/148  

Clang with libc++ and -std=c++23 fails to compile with:  
  
    error: no member named 'terminate' in namespace 'std'; did you mean 'template'?  
  
This change ensures the definition of `std::terminate()` is available for this scenario by adding the relevant include.  
  
Steps to reproduce on a mac:  
  
1. Create a file to compile: `cat test.cpp`  
  
```  
#include <boost/safe_numerics/safe_integer.hpp>  
```  
  
2.  Compile the file with clang, libc++ and c++23 standard.  Note this uses a copy of boost 1.90 installed by conan.  
  
```  
$ clang test.cpp -c -o test.o -I/Users/kalaxy/.conan2/p/b/boostb7f879e062319/p/include -std=c++23  
```  
  
3.  Observe the error messages  
  
```  
/Users/kalaxy/.conan2/p/b/boostb7f879e062319/p/include/boost/safe_numerics/checked_result_operations.hpp:40:14: error: no member named 'terminate' in namespace 'std'; did you mean 'template'?  
   40 |         std::terminate();  
      |         ~~~~~^~~~~~~~~  
      |              template  
/Users/kalaxy/.conan2/p/b/boostb7f879e062319/p/include/boost/safe_numerics/checked_result_operations.hpp:40:23: error: expected unqualified-id  
   40 |         std::terminate();  
      |                       ^  
...  
```  
  
Side note: It may be worth noting that this did work for earlier versions of the c++ standard.  For instance, `-std=c++20` works fine.  So this might have to do with some of the `constexpr` changes in c++23 which can cause earlier evaluation of templates.  
  
This fix addresses issue #139.

---
