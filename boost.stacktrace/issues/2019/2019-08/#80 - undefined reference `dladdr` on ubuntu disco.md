# #80 - undefined reference `dladdr` on ubuntu disco [Closed]

> Username: simonseyock  
> Created at: 2019-08-19 17:40:00 UTC  
> Updated at: 2019-08-23 14:24:50 UTC  
> Closed at: 2019-08-23 14:24:49 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/80  

When linking boost stacktrace on ubuntu disco I get a `undefined reference to 'dladdr'` error.  
  
I did not find out how to fix this. It might be a problem in the dependecy list of the ubuntu package though. If that is the case I am happy to report the error there.  
  
As I was working on docker anyhow I created a small repository containing Dockerfile, CMakeLists.txt, etc here: https://github.com/simonseyock/stacktrace-error  
  
To run it simply call `sudo docker-compose build && sudo docker-compose up`.  
  
```  
stacktrace-test_1  | [100%] Linking CXX executable stacktrace_error  
stacktrace-test_1  | /usr/bin/ld: CMakeFiles/stacktrace_error.dir/main.cpp.o: in function `boost::stacktrace::detail::location_from_symbol::location_from_symbol(void const*)':  
stacktrace-test_1  | main.cpp:(.text._ZN5boost10stacktrace6detail20location_from_symbolC2EPKv[_ZN5boost10stacktrace6detail20location_from_symbolC5EPKv]+0x4e): undefined reference to `dladdr'  
stacktrace-test_1  | /usr/bin/ld: CMakeFiles/stacktrace_error.dir/main.cpp.o: in function `boost::stacktrace::frame::name[abi:cxx11]() const':  
stacktrace-test_1  | main.cpp:(.text._ZNK5boost10stacktrace5frame4nameB5cxx11Ev[_ZNK5boost10stacktrace5frame4nameB5cxx11Ev]+0x31): undefined reference to `dladdr'  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-08-23 14:24:49 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/80#issuecomment-524335418  

It's in the docs: ["Requires linking with libdl library on POSIX platforms."](https://www.boost.org/doc/libs/1_71_0/doc/html/stacktrace/configuration_and_build.html)
