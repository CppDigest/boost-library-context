# #115 - clang and libbacktrace detection [Closed]

> Username: bgemmill  
> Created at: 2021-11-23 02:24:53 UTC  
> Updated at: 2023-09-27 22:47:50 UTC  
> Closed at: 2022-09-12 12:47:48 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/115  

I'm trying to build boost with clang and get the library `boost_stacktrace_backtrace` at the end.  
  
If we use a process like this to just build stacktrace:  
```  
./bootstrap.sh --with-toolset=clang  
cd libs/stacktrace/build  
../../../b2 -a libbacktrace  
```  
  
We get the error:  
`../../../libs/stacktrace/build/has_backtrace.cpp:7:10: fatal error: 'backtrace.h' file not found`  
  
Which makes sense, given that `backtrace.h` lives in gcc's library location, and not clang's.   
  
The issue is that there does not seem to be a set of b2 options that will fix this issue for clang, because [has_backtrace.cpp](https://github.com/boostorg/stacktrace/blob/develop/build/has_backtrace.cpp#L7) does not use `BOOST_STACKTRACE_BACKTRACE_INCLUDE_FILE` like the [rest of stacktrace does](https://github.com/boostorg/stacktrace/blob/develop/include/boost/stacktrace/detail/libbacktrace_impls.hpp#L20-L24).  
  
This means that even if a user tries to point b2 to libbacktrace via `define=BOOST_STACKTRACE_BACKTRACE_INCLUDE_FILE=...` as documented [here](https://www.boost.org/doc/libs/1_77_0/doc/html/stacktrace/configuration_and_build.html#ftn.stacktrace.configuration_and_build.f3), has_backtrace.cpp will still fail to detect anything.  
  
Could has_backtrace.cpp please be changed to use BOOST_STACKTRACE_BACKTRACE_INCLUDE_FILE?

---

## Comment 1

> Username: apolukhin  
> Created at: 2022-09-12 12:48:14 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/115#issuecomment-1243691893  

Many thanks for the bugreport!

---

## Comment 2

> Username: bgemmill  
> Created at: 2022-09-12 13:51:30 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/115#issuecomment-1243775438  

Thanks for fixing that!
