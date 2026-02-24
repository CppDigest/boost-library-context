# #97 - Document more precisely CMake instructions [Closed]

> Username: sehe  
> Created at: 2022-05-22 09:31:53 UTC  
> Updated at: 2022-05-28 19:46:58 UTC  
> Closed at: 2022-05-28 19:46:58 UTC  
> Url: https://github.com/boostorg/mysql/issues/97  

The CMake support is short and sweet, but the prose description in the Readme leaves more room for error than desirable.  
  
Also, giving a succinct example instead of the prose instruction helps people not familiar with CMake.  
  
In my case I interpreted "[Finally, link your target against the Boost::mysql interface library, and you will be done](https://github.com/anarthal/mysql/#:~:text=Finally%2C%20link%20your%20target%20against%20the%20Boost%3A%3Amysql%20interface%20library%2C%20and%20you%20will%20be%20done)!" as follows:  
  
```cmake  
cmake_minimum_required(VERSION 3.22)  
project(minimal)  
  
add_subdirectory(deps/mysql)  
  
find_package(Boost 1.72.0 REQUIRED COMPONENTS system)  
  
link_libraries(hello_world Boost::mysql)  
add_executable(hello_world hello_world.cpp)  
```  
  
This normally works and is my default setup for proof-of-concept/support tasks because `link_libraries` applies to all targets. However, with Boost::mysql being header only, `target_link_libraries` apparently makes a difference and adds (surprise) include directories rather than link targets.  
  
Now, I'm not sure whether `link_libraries` should also have worked, can work at all. If so, then that's a nice improvement. If not, I suggest more accurate documentation of what is expected/recommended.

---

## Comment 1

> Username: anarthal  
> Created at: 2022-05-28 19:28:01 UTC  
> Url: https://github.com/boostorg/mysql/issues/97#issuecomment-1140318130  

I've tested the above code snippet and it fails due to `hello_word` linking to itself. You should remove it from the `link_libraries` command, as `link_libraries` doesn't apply to a particular target.  
  
The following works for me (I'm using a slightly older CMake):  
  
```  
cmake_minimum_required(VERSION 3.16)  
project(minimal)  
  
add_subdirectory(deps/mysql)  
  
find_package(Boost 1.72.0 REQUIRED COMPONENTS system)  
  
link_libraries(Boost::mysql)  
add_executable(hello_world hello_world.cpp)  
```  
  
Both `link_libraries` and `target_link_libraries` should work the same. Could you please confirm this change solves the issue?

---

## Comment 2

> Username: sehe  
> Created at: 2022-05-28 19:46:58 UTC  
> Url: https://github.com/boostorg/mysql/issues/97#issuecomment-1140320789  

:derp: Yeah, I obviously didn't originally had that silly copy/paste error.  
  
Come to think of it, my original issue *might* very well be because I was using 53da045 originally (as I still had it from last year when I first looked at it).  
  
That seems a reasonable explanation for my experience there. So, closing as PEBCAK :)
