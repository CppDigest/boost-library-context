# #337 - Appropriate version of ruby not picked up [Closed]

> Username: shreyans800755  
> Created at: 2017-04-14 21:34:18 UTC  
> Updated at: 2017-04-19 20:26:24 UTC  
> Closed at: 2017-04-19 20:26:24 UTC  
> Url: https://github.com/boostorg/hana/issues/337  

Although appropriate version of ruby is installed, cmake is not able to pick it up for benchmark purpose and giving warning while running cmake command.  
  
  
Details:  
Output of different commands:  
`ruby -v`  
  
> ruby 2.2.6p396 (2016-11-15 revision 56800) [x86_64-linux-gnu]  
  
`g++ -v`  
  
> gcc version 6.2.0 20160901 (Ubuntu 6.2.0-3ubuntu11~14.04)  
  
`~/git/boost/hana/build$ cmake .. -DMAKE_CXX_COMPILER=/usr/bin/g++`  
Relevant part of output:  
  
> -- Could NOT find Ruby (missing:  RUBY_INCLUDE_DIR RUBY_LIBRARY RUBY_CONFIG_INCLUDE_DIR) (found suitable version "2.2.0", minimum required is "2.1")  
CMake Warning at benchmark/CMakeLists.txt:10 (message):  
  Ruby >= 2.1 was not found; the benchmarks will be unavailable.  
  
As per output shown above, it seems like ruby 2.2 is found as suitable by cmake. But still it is evaluating 2.2 >= 2.1 as false.

---

## Comment 1

> Username: ldionne  
> Created at: 2017-04-17 08:01:50 UTC  
> Url: https://github.com/boostorg/hana/issues/337#issuecomment-294428057  

Can you explain how you installed Ruby?

---

## Comment 2

> Username: ldionne  
> Created at: 2017-04-17 08:08:33 UTC  
> Url: https://github.com/boostorg/hana/issues/337#issuecomment-294428820  

Also, can you please run `cmake .. -D_RUBY_DEBUG_OUTPUT=true` and paste the output here? It looks like you don't have the Ruby headers and the Ruby library, and that causes `find_package(Ruby ...)` to report that it has not found the proper Ruby version. Hana then simply prints an incorrect warning, assuming the only reason to fail would be a wrong or missing Ruby version.

---

## Comment 3

> Username: shreyans800755  
> Created at: 2017-04-17 20:21:05 UTC  
> Updated at: 2017-04-17 20:21:26 UTC  
> Url: https://github.com/boostorg/hana/issues/337#issuecomment-294580387  

As far as I can remember, I installed it using apt-get install ruby2.2.  
And updated default version to be used by rvm to ruby 2.2 and also purged the older version(1.9.1).  
  
Output of  
`cmake .. -D_RUBY_DEBUG_OUTPUT=true`  
  
> -- Could NOT find Boost  
> CMake Warning at CMakeLists.txt:122 (message):  
>   The rest of Boost was not found; some tests and examples will be disabled.  
>   
>   
> -- Found Valgrind: /usr/bin/valgrind  
> -- --------FindRuby.cmake debug------------  
> -- _RUBY_POSSIBLE_EXECUTABLE_NAMES: ruby2.1;ruby21;ruby;ruby2.0;ruby20;ruby1.9;ruby19;ruby1.8;ruby18  
> -- _RUBY_POSSIBLE_LIB_NAMES: ruby;ruby-static;ruby2.2;ruby22;ruby-2.2;ruby-2.2.0  
> -- RUBY_ARCH_DIR: /usr/lib/x86_64-linux-gnu/ruby/2.2.0  
> -- RUBY_HDR_DIR: /usr/include/ruby-2.2.0  
> -- RUBY_POSSIBLE_LIB_DIR: /usr/lib  
> -- Found RUBY_VERSION: "2.2.0" , short: "2.2", nodot: "22"  
> -- _RUBY_REQUIRED_VARS: RUBY_EXECUTABLE;RUBY_INCLUDE_DIR;RUBY_LIBRARY;RUBY_CONFIG_INCLUDE_DIR  
> -- RUBY_EXECUTABLE: /usr/bin/ruby  
> -- RUBY_LIBRARY: RUBY_LIBRARY-NOTFOUND  
> -- RUBY_INCLUDE_DIR: RUBY_INCLUDE_DIR-NOTFOUND  
> -- RUBY_CONFIG_INCLUDE_DIR: RUBY_CONFIG_INCLUDE_DIR-NOTFOUND  
> -- --------------------  
> -- Could NOT find Ruby (missing:  RUBY_INCLUDE_DIR RUBY_LIBRARY RUBY_CONFIG_INCLUDE_DIR) (found suitable version "2.2.0", minimum required is "2.1")  
> CMake Warning at benchmark/CMakeLists.txt:10 (message):  
>   Ruby >= 2.1 was not found; the benchmarks will be unavailable.  
>   
>   
> -- Could NOT find Doxygen (missing:  DOXYGEN_EXECUTABLE)   
> CMake Warning at doc/CMakeLists.txt:7 (message):  
>   Doxygen was not found; the 'doc' and 'docset' targets will be unavailable.  
>   
>   
> -- Configuring done  
> -- Generating done  
> -- Build files have been written to: /home/thereal/git/boost/hana

---

## Comment 4

> Username: girishramnani  
> Created at: 2017-04-18 22:52:17 UTC  
> Url: https://github.com/boostorg/hana/issues/337#issuecomment-295007835  

could you try installing the `ruby2.2-dev` package.

---

## Comment 5

> Username: shreyans800755  
> Created at: 2017-04-19 20:26:24 UTC  
> Url: https://github.com/boostorg/hana/issues/337#issuecomment-295426057  

ruby2.2-dev and installation of couple of gems solved the issue.
