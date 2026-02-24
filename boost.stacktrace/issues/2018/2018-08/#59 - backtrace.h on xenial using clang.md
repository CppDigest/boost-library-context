# #59 - backtrace.h on xenial using clang [Closed]

> Username: antoshkka1  
> Created at: 2018-08-02 09:49:27 UTC  
> Updated at: 2018-08-07 19:29:10 UTC  
> Closed at: 2018-08-07 19:29:10 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/59  

include/boost/stacktrace/detail/libbacktrace_impls.hpp  
```  
#ifdef BOOST_STACKTRACE_BACKTRACE_INCLUDE_FILE  
#   include BOOST_STACKTRACE_BACKTRACE_INCLUDE_FILE  
#else  
#   include <backtrace.h>  
#endif  
```  
  
On Xenial it could be found in /usr/lib/gcc/x86_64-linux-gnu/5/include/backtrace.h
