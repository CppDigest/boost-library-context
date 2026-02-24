# #45 - conflict with gcc 4.8 cxxabi.h files. [Closed]

> Username: robertramey  
> Created at: 2018-12-18 16:55:57 UTC  
> Updated at: 2018-12-18 17:51:56 UTC  
> Closed at: 2018-12-18 17:51:56 UTC  
> Url: https://github.com/boostorg/core/issues/45  

I moved to update the serialization library and got a couple of new test failures on gcc 4.8  
  
./boost/core/uncaught_exceptions.hpp:52:18: error: functions that differ only in their return type cannot be overloaded  
extern "C" void* __cxa_get_globals();  
           ~~~~~ ^  
/usr/lib/gcc/x86_64-linux-gnu/4.8/../../../../include/c++/4.8/cxxabi.h:595:3: note: previous declaration is here  
  __cxa_get_globals() _GLIBCXX_NOTHROW __attribute__ ((__const__));  
  ^  
1 error generated.
