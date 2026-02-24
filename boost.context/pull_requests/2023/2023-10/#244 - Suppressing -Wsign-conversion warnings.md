# #244 Suppressing -Wsign-conversion warnings [Merged]

> Username: the-moisrex  
> Created at: 2023-10-29 05:05:55 UTC  
> Updated at: 2023-11-08 07:34:41 UTC  
> Merged at: 2023-11-08 07:34:41 UTC  
> Closed at: 2023-11-08 07:34:41 UTC  
> Url: https://github.com/boostorg/context/pull/244  

Casting them out will fix the problem easily. I'm also gonna open a pull request for [Boost::coroutine](https://github.com/boostorg/coroutine/tree/develop) warnings as well.  
  
g++ (GCC) 13.2.1 20230801  
  
```c++  
[17/27] Building CXX object _deps/boost_context-build/CMakeFiles/boost_context.dir/src/posix/stack_traits.cpp.o  
/webpp/build-dev-clang/_deps/boost_context-src/src/posix/stack_traits.cpp:42:12: warning: implicit conversion changes signedness: 'long' to 'std::size_t' (aka 'unsigned long') [-Wsign-conversion]  
    return ::sysconf( _SC_PAGESIZE);  
    ~~~~~~ ^~~~~~~~~~~~~~~~~~~~~~~~  
/webpp/build-dev-clang/_deps/boost_context-src/src/posix/stack_traits.cpp:80:12: warning: implicit conversion changes signedness: 'long' to 'std::size_t' (aka 'unsigned long') [-Wsign-conversion]  
    return MINSIGSTKSZ;  
    ~~~~~~ ^~~~~~~~~~~  
/usr/include/bits/sigstksz.h:32:22: note: expanded from macro 'MINSIGSTKSZ'  
# define MINSIGSTKSZ SIGSTKSZ  
                     ^~~~~~~~  
/usr/include/bits/sigstksz.h:28:19: note: expanded from macro 'SIGSTKSZ'  
# define SIGSTKSZ sysconf (_SC_SIGSTKSZ)  
                  ^~~~~~~~~~~~~~~~~~~~~~  
2 warnings generated.  
[19/27] Building CXX object _deps/boost_coroutine-build/CMakeFiles/boost_coroutine.dir/src/posix/stack_traits.cpp.o  
/webpp/build-dev-clang/_deps/boost_coroutine-src/src/posix/stack_traits.cpp:38:12: warning: implicit conversion changes signedness: 'long' to 'std::size_t' (aka 'unsigned long') [-Wsign-conversion]  
    return ::sysconf( _SC_PAGESIZE);  
    ~~~~~~ ^~~~~~~~~~~~~~~~~~~~~~~~  
/webpp/build-dev-clang/_deps/boost_coroutine-src/src/posix/stack_traits.cpp:89:10: warning: implicit conversion changes signedness: 'long' to 'std::size_t' (aka 'unsigned long') [-Wsign-conversion]  
{ return SIGSTKSZ; }  
  ~~~~~~ ^~~~~~~~  
/usr/include/bits/sigstksz.h:28:19: note: expanded from macro 'SIGSTKSZ'  
# define SIGSTKSZ sysconf (_SC_SIGSTKSZ)  
                  ^~~~~~~~~~~~~~~~~~~~~~  
2 warnings generated.  
```

---
