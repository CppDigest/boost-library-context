# #44 - Support for QNX 7 [Open]

> Username: MarkusRJK  
> Created at: 2018-01-25 09:40:56 UTC  
> Updated at: 2018-04-15 10:05:12 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/44  

Since boost shall be platform independent and QNX 7 is a Unix variant, would there be support for QNX 7? I tried to use boost/stacktrace with QNX 7. Just including boost/stacktrace.hpp resulted in a compilation error because the internally included backtrace.h (from QNX 7) did not resolve the required symbols. Comparing QNX7's backtrace.h with Windows' backtrace.h made obvious that it could not compile.  
  
[QNX7-backtrace.zip](https://github.com/boostorg/stacktrace/files/1663383/QNX7-backtrace.zip)
