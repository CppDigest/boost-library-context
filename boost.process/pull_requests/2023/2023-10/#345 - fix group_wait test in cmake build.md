# #345 fix group_wait test in cmake build. [Merged]

> Username: iskunk  
> Created at: 2023-10-05 19:42:47 UTC  
> Updated at: 2023-10-09 02:46:03 UTC  
> Merged at: 2023-10-09 02:46:03 UTC  
> Closed at: 2023-10-09 02:46:03 UTC  
> Url: https://github.com/boostorg/process/pull/345  

Ran into this error in a CMake build of the Git source with `-DBUILD_TESTING=ON`:  
```  
[ 46%] Building CXX object libs/process/test/CMakeFiles/boost_process_group_wait.dir/group_wait.cpp.o  
In file included from /tmp/boost/libs/system/include/boost/system/error_category.hpp(11),  
                 from /tmp/boost/libs/system/include/boost/system/error_code.hpp(14),  
                 from /tmp/boost/libs/process/test/group_wait.cpp(15):  
/tmp/boost/libs/system/include/boost/system/detail/error_category_impl.hpp(141): warning #2196: routine is both "inline" and "noinline"  
  inline BOOST_NOINLINE error_category::operator std::error_category const & () const  
         ^  
  
/tmp/boost/libs/process/test/group_wait.cpp(19): catastrophic error: cannot open source file "/tmp/boost/libs/process/test/group_wait.cpp"  
  #include <boost/scope_exit.hpp>  
                                 ^  
  
compilation aborted for /tmp/boost/libs/process/test/group_wait.cpp (code 4)  
make[2]: *** [libs/process/test/CMakeFiles/boost_process_group_wait.dir/build.make:76: libs/process/test/CMakeFiles/boost_process_group_wait.dir/group_wait.cpp.o] Error 4  
make[1]: *** [CMakeFiles/Makefile2:37090: libs/process/test/CMakeFiles/boost_process_group_wait.dir/all] Error 2  
make: *** [Makefile:146: all] Error 2  
```  
  
Not sure I added `Boost::scope_exit` in the right place, but this at least got the build working again for me.

---
