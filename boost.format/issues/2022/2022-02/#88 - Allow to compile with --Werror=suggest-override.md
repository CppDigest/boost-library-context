# #88 - Allow to compile with "-Werror=suggest-override" [Closed]

> Username: pcaspers  
> Created at: 2022-02-10 15:32:01 UTC  
> Updated at: 2022-02-10 20:38:18 UTC  
> Closed at: 2022-02-10 20:38:18 UTC  
> Url: https://github.com/boostorg/format/issues/88  

Newer versions of clang / gcc support a suggest-override warning: If you have a file `format_error.cpp`  
  
```  
#include <boost/format.hpp>  
```  
  
the compilation with e.g. Apple clang version 13.0.0 (clang-1300.0.29.3)  
  
```  
clang++ -std=c++11 -c -Werror=suggest-override format_error.cpp  
```  
  
will produce  
  
```  
In file included from format_error.cpp:1:  
In file included from /usr/local/include/boost/format.hpp:44:  
/usr/local/include/boost/format/exceptions.hpp:33:33: error: 'what' overrides a member function but is not marked 'override' [-Werror,-Wsuggest-override]  
            virtual const char *what() const throw() {  
                                ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/exception:104:25: note: overridden virtual function is here  
    virtual const char* what() const _NOEXCEPT;  
                        ^  
```
