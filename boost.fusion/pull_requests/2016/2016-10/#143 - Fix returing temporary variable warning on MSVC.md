# #143 Fix returing temporary variable warning on MSVC. [Merged]

> Username: Flast  
> Created at: 2016-10-31 17:44:43 UTC  
> Updated at: 2016-10-31 17:45:00 UTC  
> Merged at: 2016-10-31 17:44:58 UTC  
> Closed at: 2016-10-31 17:44:58 UTC  
> Url: https://github.com/boostorg/fusion/pull/143  

This PR will fix following warning on MSVC.  
  
> boost/fusion/container/deque/detail/keyed_element.hpp(93) : warning C4172: returning address of local variable or temporary  
  
Additionally, correct test condition that tried to use `std::tuple` without compiler support.  
  
Tested  
- GCC 6.2.1 20160916 - fedora rawhide  
    - gnu++98 / gnu++11 / gnu++14  
- Clang 3.8.0 - fedora rawhide  
    - gnu++98 / gnu++11 / gnu++14  
- MSVC  
    - 2008sp1 / 2010sp1 / 2012u5 / 2013u5 / 2015u3

---
