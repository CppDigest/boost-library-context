# #94 - macOS _GNU_SOURCE linking error [Closed]

> Username: oguzziya  
> Created at: 2020-06-22 23:17:20 UTC  
> Updated at: 2021-11-18 12:49:46 UTC  
> Closed at: 2021-11-18 12:49:46 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/94  

Boost version: 1.72.03 (Installed by homebrew)  
OS: Catalina 10.15.5  
Compiler: g++9  
  
I get the following error in the compile time:  
```  
#error "Boost.Stacktrace requires `_Unwind_Backtrace` function. Define `_GNU_SOURCE` macro or `BOOST_STACKTRACE_GNU_SOURCE_NOT_REQUIRED` if _Unwind_Backtrace is available without `_GNU_SOURCE`."  
      |  ^~~~~  
```  
When I tried to define the `_GNU_SOURCE`, the error disappears and I get the linking error, mostly related to ` boost::log::v2_mt_posix`. I could not further track down the problem. There was a similar issue #88 also.
