# #65 - Jamfile should require C++20 [Closed]

> Username: anarthal  
> Created at: 2023-08-10 14:22:09 UTC  
> Updated at: 2023-10-17 11:23:20 UTC  
> Closed at: 2023-10-17 11:23:20 UTC  
> Url: https://github.com/boostorg/cobalt/issues/65  

Since the required C++ standard is high, the Jamfile should enforce it. You currently got a bunch of long errors if not specifying `cxxstd=20`.  
  
This is likely going to break the Boost build for cxx < 20 if not handled correctly.
