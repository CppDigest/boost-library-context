# #127 - CMakeLists.txt is incompatible with Boost cmake [Closed]

> Username: anarthal  
> Created at: 2024-02-28 12:25:03 UTC  
> Updated at: 2024-03-25 03:54:13 UTC  
> Closed at: 2024-03-25 03:54:13 UTC  
> Url: https://github.com/boostorg/parser/issues/127  

Guidelines listed here: https://github.com/boostorg/cmake/blob/develop/developer.md  
  
I plugged Parser into Boost.MySQL CIs, some of which perform CMake workflow testing, and they failed. Note that Boost CIs don't generally have gtest installed on them (I don't know even if it's allowed due to licensing).  
  
Build (edited link): https://drone.cpp.al/boostorg/mysql/545/7/3

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-29 00:01:03 UTC  
> Url: https://github.com/boostorg/parser/issues/127#issuecomment-1970133707  

The CMake files and all the test code is going to be tossed aside if/when the library is accepted.
