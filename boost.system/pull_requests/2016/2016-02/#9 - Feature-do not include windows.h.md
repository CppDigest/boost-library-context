# #9 Feature/do not include windows.h [Merged]

> Username: jlodos  
> Created at: 2016-02-19 04:05:10 UTC  
> Updated at: 2016-02-24 01:05:16 UTC  
> Merged at: 2016-02-22 15:36:07 UTC  
> Closed at: 2016-02-22 15:36:07 UTC  
> Url: https://github.com/boostorg/system/pull/9  



---

## Comment 1

> Username: Beman  
> Created_at: 2016-02-22 15:36:40 UTC  
> Url: https://github.com/boostorg/system/pull/9#issuecomment-187233783  

Merged. Thanks!  
  
--Beman

---

## Comment 2

> Username: Beman  
> Created_at: 2016-02-23 22:54:34 UTC  
> Url: https://github.com/boostorg/system/pull/9#issuecomment-187952154  

I had to revert this pull request. It worked fine with the msvc toolset, testing with the last four VC++ versions. But it failed with GCC version 4.9.3 (cygwin64). Below are the first few error messages.   
  
Please figure out the fix, and then submit another pull request.  
  
Thanks,  
  
--Beman  
  
gcc.compile.c++ ......\bin.v2\libs\system\build\gcc-c++03\debug\link-static\error_code.o  
In file included from ....../boost/system/detail/error_code.ipp:25:0,  
                 from ......\libs\system\src\error_code.cpp:19:  
....../boost/detail/winapi/error_handling.hpp: In function 'boost::detail::winapi::WORD_ boost::detail::winapi::MAKELANGID_(boost::detail::winapi::WORD_, boost::detail::winapi::WORD_)':  
....../boost/detail/winapi/error_handling.hpp:97:46: warning: conversion to 'boost::detail::winapi::WORD_ {aka short unsigned int}' from 'int' may alter its value [-Wconversion]  
     return ((((WORD_)(s)) << 10) | (WORD_)(p));  
                                              ^  
In file included from ....../boost/system/detail/error_code.ipp:24:0,  
                 from ......\libs\system\src\error_code.cpp:19:  
....../boost/system/detail/error_code.ipp: In member function 'virtual boost::system::error_condition boost::system::{anonymous}::system_error_category::default_error_condition(int) const':  
....../boost/system/detail/error_code.ipp:284:10: error: a function call cannot appear in a constant-expression  
     case ERROR_ACCESS_DENIED: return make_error_condition( permission_denied );  
          ^  
....../boost/system/detail/error_code.ipp:285:10: error: a function call cannot appear in a constant-expression  
     case ERROR_ALREADY_EXISTS: return make_error_condition( file_exists );  
          ^  
....../boost/system/detail/error_code.ipp:286:10: error: a function call cannot appear in a constant-expression  
     case ERROR_BAD_UNIT: return make_error_condition( no_such_device );  
          ^  
....../boost/system/detail/error_code.ipp:287:10: error: a function call cannot appear in a constant-expression  
     case ERROR_BUFFER_OVERFLOW: return make_error_condition( filename_too_long );  
          ^

---

## Comment 3

> Username: jlodos  
> Created_at: 2016-02-23 23:23:31 UTC  
> Url: https://github.com/boostorg/system/pull/9#issuecomment-187962375  

The problem is in boost::detail::winapi. I have already submitted a pull request there. If/when it is merged I will make a new pull request. Thanks.

---

## Comment 4

> Username: jlodos  
> Created_at: 2016-02-24 01:05:16 UTC  
> Url: https://github.com/boostorg/system/pull/9#issuecomment-187995722  

There was also an error including winerror in the wrong order. It is not needed to include winerror.h at all, boost::detail::winapi takes care of that. Thanks.

---
