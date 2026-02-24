# #129 - Unprotected use of std::default_initializable in char8_view [Closed]

> Username: anarthal  
> Created at: 2024-02-28 12:42:35 UTC  
> Updated at: 2024-03-15 21:29:31 UTC  
> Closed at: 2024-03-15 21:29:30 UTC  
> Url: https://github.com/boostorg/parser/issues/129  

In file: `./boost/parser/detail/text/transcode_view.hpp:331:46`  
  
Build can be found here (edited link): https://drone.cpp.al/boostorg/mysql/545/12/3  
  
Compiler details:  
* toolset=clang-11  
* cxxstd=20  
* variant=debug  
* warnings=extra  
* OS: Ubuntu 22.04

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-29 04:45:01 UTC  
> Url: https://github.com/boostorg/parser/issues/129#issuecomment-1970387216  

How the hell did I miss exactly one of three default ctors?!  The other two are qritten correctly.  Anyway, this is now fixed on a branch.
