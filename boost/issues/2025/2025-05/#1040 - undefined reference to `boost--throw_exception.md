# #1040 - undefined reference to `boost::throw_exception [Closed]

> Username: Raimo33  
> Created at: 2025-05-11 20:46:05 UTC  
> Updated at: 2025-05-13 06:27:45 UTC  
> Closed at: 2025-05-13 06:27:45 UTC  
> Url: https://github.com/boostorg/boost/issues/1040  

when linking against static .a boost_system.a or any other boost library, i get:  
  
``<artificial>:(.text+0x835b2): undefined reference to `boost::throw_exception(std::exception const&, boost::source_location const&)'  
collect2: error: ld returned 1 exit status``  
  
it is fixed when linking dynamic .so files  
  
https://stackoverflow.com/questions/50133783/c-cmake-build-error-undefined-reference-to-boostthrow-exceptionstdexcep  
  
FIXED: it happens when you compile with -fno-exceptions. Boost wants you to define "throw_exception"
