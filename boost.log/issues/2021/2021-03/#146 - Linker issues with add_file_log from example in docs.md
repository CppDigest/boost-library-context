# #146 - Linker issues with add_file_log from example in docs [Closed]

> Username: createyourpersonalaccount  
> Created at: 2021-03-22 01:35:44 UTC  
> Updated at: 2021-03-22 01:52:29 UTC  
> Closed at: 2021-03-22 01:52:29 UTC  
> Url: https://github.com/boostorg/log/issues/146  

I have tried to use cmake with:  
  
    cmake_minimum_required(VERSION 3.11)  
    project(logger)  
    add_executable(logger tutorial_file.cpp)  
    find_package(Boost REQUIRED COMPONENTS log)  
    target_link_libraries(logger ${Boost_LIBRARIES})  
  
on the file https://www.boost.org/doc/libs/release/libs/log/example/doc/tutorial_file.cpp  
  
However, it appears that the appearance of `boost::log::add_file_log()` results in linker errors (the simpler logger appearing earlier in the documentation that did not include it compiled fine)  
  
    $ cmake --build .                                                                   
    [ 50%] Linking CXX executable logger  
      
    _mt_posix::basic_formatter<char> boost::log::v2_mt_posix::parse_formatter<char>(char const*)':  
    tutorial_file.cpp:  
    (.text._ZN5boost3log11v2_mt_posix15parse_formatterIcEENS1_15basic_formatterIT_EEPKS4_[_ZN5boost3log11v2_mt_posix15parse_formatterIcEENS1_15basic_formatterIT_EEPKS4_]+0x45):  
    undefined reference to `boost::log::v2_mt_posix::basic_formatter<char> boost::log::v2_mt_posix::parse_formatter<char>(char const*, char const*)'                                             
    collect2: error: ld returned 1 exit status  
    make[2]: *** [CMakeFiles/logger.dir/build.make:91: logger] Error 1  
    make[1]: *** [CMakeFiles/Makefile2:76: CMakeFiles/logger.dir/all] Error 2  
    make: *** [Makefile:84: all] Error 2  
  
I have tried variations on the `CMakeLists.txt` configuration file of cmake to set static or dynamic linking, and other things I've found online, but nothing seems to fix the issue. I am using `libboost-log1.71-dev` and `libboost-log1.71.0` on Ubuntu 20.04, see https://packages.ubuntu.com/focal/libboost-all-dev  
  
Help is appreciated.

---

## Comment 1

> Username: createyourpersonalaccount  
> Created at: 2021-03-22 01:52:29 UTC  
> Url: https://github.com/boostorg/log/issues/146#issuecomment-803710556  

I fixed the problem by adding `log_setup` into `find_package()`. This is my `CMakeLists.txt` that makes it work:  
  
    cmake_minimum_required(VERSION 3.11)  
    project(logger)  
    add_executable(logger tutorial_file.cpp)  
    find_package(Boost REQUIRED COMPONENTS log log_setup)  
    target_link_libraries(logger ${Boost_LIBRARIES})
