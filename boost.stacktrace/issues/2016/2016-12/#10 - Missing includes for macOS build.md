# #10 - Missing includes for macOS build [Closed]

> Username: ghost  
> Created at: 2016-12-19 21:04:33 UTC  
> Updated at: 2017-01-03 11:27:44 UTC  
> Closed at: 2017-01-03 11:27:25 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/10  

I attempted to build this library on macOS (Xcode 7.3.1) and found that there were a few includes missing:  
  
- `example/assert_handler.cpp`: missing `<cstdlib>` for `std::exit()`  
- `example/terminate_handler.cpp`: missing `<cstdlib>` for `std::exit()`  
- `include/boost/stacktrace/detail/backend_linux.hpp`: missing `<signal.h>` for `kill()`  
  
Aside from that the code seems to work great on macOS (tested on 10.11.6 with Xcode 7.3.1 -- clang-703.0.31) using the Linux backend, based on my limited testing using your examples and test code.  
  
Thanks!

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-01-03 11:27:25 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/10#issuecomment-270095504  

Fixed.  
  
Great thanks for the report and hints on how to fix the issue.
