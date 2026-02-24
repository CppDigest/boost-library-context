# #52 Update support for standard containers. [Merged]

> Username: Flast  
> Created at: 2017-05-16 15:10:37 UTC  
> Updated at: 2017-05-16 15:37:23 UTC  
> Merged at: 2017-05-16 15:37:19 UTC  
> Closed at: 2017-05-16 15:37:20 UTC  
> Url: https://github.com/boostorg/phoenix/pull/52  

This PR includes following features:  
- Added support for `std::set` and `std::multiset`.  
- Fixed [#7423](https://svn.boost.org/trac/boost/ticket/7423) `std::map::erase returns iterator in C++11 mode instead of void, should be handled properly` .  
- Added tests for C++11 unordered associative containers.  
  
Tested:  
- GCC 7.1.1 20170503 (Red Hat 7.1.1-1)  
    - gnu++98 / 11 / 14 / 1z  
- Clang 4.0.0  
    - gnu++98 / 11 / 14 / 1z  
- MSVC  
    - 8 / 9sp1 / 10sp1 / 11u5 / 12u5 / 14u3 / 14.1

---
