# #298 - Boost.Fiber header compile fails with GCC Compile option -Werror=conversion [Open]

> Username: ClaymorePT  
> Created at: 2022-06-05 14:13:56 UTC  
> Updated at: 2022-06-05 14:14:14 UTC  
> Url: https://github.com/boostorg/fiber/issues/298  

Hello!  
I'm creating this issue just raise awareness about what happens when compiling Boost.Fiber headers along with the GCC flag `-Werror=conversion`.  
It may or not be important for the community. For me it is, given the compilation restrictions which I'm applying to my project.  
```  
[build] /usr/bin/g++ -DBOOST_STACKTRACE_ADDR2LINE_LOCATION=\"/usr/bin/addr2line\" -DBOOST_STACKTRACE_LINK -DBOOST_STACKTRACE_USE_ADDR2LINE -DBOOST_STACKTRACE_USE_BACKTRACE -DBOOST_STACKTRACE_USE_NOOP -DSPDLOG_COMPILED_LIB -DSPDLOG_FMT_EXTERNAL -I/home/claymore/.conan/data/boost/1.79.0/_/_/package/1671931156455a119d7c3f14d951ac5fdbc5cd10/include -I/home/claymore/.conan/data/spdlog/1.10.0/_/_/package/0ad8763f0289ded6e39ff258ec01c35b1aac6aa2/include -I/home/claymore/.conan/data/zlib/1.2.12/_/_/package/d13c97721d7bdc192ca6529684f2b79beeae8a7c/include -I/home/claymore/.conan/data/bzip2/1.0.8/_/_/package/ad18e1e856ff78d73ad6f3c9a214f85abf2d0550/include -I/home/claymore/.conan/data/libbacktrace/cci.20210118/_/_/package/d13c97721d7bdc192ca6529684f2b79beeae8a7c/include -I/home/claymore/.conan/data/fmt/8.1.1/_/_/package/0d8b943d676dc202f180a2598d04457e173d7b97/include -I/home/claymore/Desktop/CppServiceBootstrap/src/include -g  -DSPDLOG_FMT_EXTERNAL -DSPDLOG_COMPILED_LIB -DBOOST_STACKTRACE_ADDR2LINE_LOCATION=\"/usr/bin/addr2line\" -DBOOST_STACKTRACE_USE_ADDR2LINE -DBOOST_STACKTRACE_USE_BACKTRACE -DBOOST_STACKTRACE_USE_NOOP -Winvalid-pch -pedantic-errors -Wall -Wextra -Wformat=2 -Wformat-signedness -Wformat-truncation=2 -Wformat-overflow=2 -Wconversion -Wfatal-errors -Wdouble-promotion -Wfloat-equal -Werror=cast-qual -Werror=cast-align -Werror=overflow -Werror=conversion -Werror=nonnull -Werror=nonnull-compare -Werror=null-dereference -Werror=init-self -Werror=uninitialized -Werror=invalid-memory-model -fno-stack-protector -fno-pie -rdynamic -O0 -ggdb3 -fno-inline -ftest-coverage --coverage -fsanitize=thread -fsanitize=shift -fsanitize=shift-exponent -fsanitize=shift-base -fsanitize=integer-divide-by-zero -fsanitize=unreachable -fsanitize=vla-bound -fsanitize=null -fstack-protector-all -fstack-clash-protection -std=gnu++20 -MD -MT CMakeFiles/CppServiceBootstrap.dir/src/executable/CppServiceBootstrap.cpp.o -MF CMakeFiles/CppServiceBootstrap.dir/src/executable/CppServiceBootstrap.cpp.o.d -o CMakeFiles/CppServiceBootstrap.dir/src/executable/CppServiceBootstrap.cpp.o -c /home/claymore/Desktop/CppServiceBootstrap/src/executable/CppServiceBootstrap.cpp  
[build] In file included from /home/claymore/.conan/data/boost/1.79.0/_/_/package/1671931156455a119d7c3f14d951ac5fdbc5cd10/include/boost/fiber/detail/spinlock_ttas_adaptive_futex.hpp:18,  
[build]                  from /home/claymore/.conan/data/boost/1.79.0/_/_/package/1671931156455a119d7c3f14d951ac5fdbc5cd10/include/boost/fiber/detail/spinlock.hpp:19,  
[build]                  from /home/claymore/.conan/data/boost/1.79.0/_/_/package/1671931156455a119d7c3f14d951ac5fdbc5cd10/include/boost/fiber/detail/data.hpp:13,  
[build]                  from /home/claymore/.conan/data/boost/1.79.0/_/_/package/1671931156455a119d7c3f14d951ac5fdbc5cd10/include/boost/fiber/context.hpp:37,  
[build]                  from /home/claymore/.conan/data/boost/1.79.0/_/_/package/1671931156455a119d7c3f14d951ac5fdbc5cd10/include/boost/fiber/algo/round_robin.hpp:16,  
[build]                  from /home/claymore/.conan/data/boost/1.79.0/_/_/package/1671931156455a119d7c3f14d951ac5fdbc5cd10/include/boost/fiber/all.hpp:11,  
[build]                  from /home/claymore/Desktop/CppServiceBootstrap/src/executable/CppServiceBootstrap.cpp:8:  
[build] /home/claymore/.conan/data/boost/1.79.0/_/_/package/1671931156455a119d7c3f14d951ac5fdbc5cd10/include/boost/fiber/detail/futex.hpp: In function ‘int boost::fibers::detail::sys_futex(void*, int32_t, int32_t)’:  
[build] /home/claymore/.conan/data/boost/1.79.0/_/_/package/1671931156455a119d7c3f14d951ac5fdbc5cd10/include/boost/fiber/detail/futex.hpp:35:21: error: conversion from ‘long int’ to ‘int’ may change value [-Werror=conversion]  
[build]    35 |     return ::syscall( SYS_futex, addr, op, x, nullptr, nullptr, 0);  
[build]       |            ~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
[build] compilation terminated due to -Wfatal-errors.  
```
