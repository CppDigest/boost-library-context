# #40 - find_package(boost COMPONENTS <long_list> CONFIG) is slow and can run out of stack space [Closed]

> Username: Neumann-A  
> Created at: 2023-07-06 09:16:54 UTC  
> Updated at: 2023-10-23 14:36:34 UTC  
> Closed at: 2023-10-23 14:36:33 UTC  
> Url: https://github.com/boostorg/cmake/issues/40  

In generell the generated cmake config files do recursive lookup of already look up boost modules which will need to excessive nesting and can even make CMake run out of stack space. I simply added a guard around the generated `find_dependency(boost_<module>)` calls so that it generates i.e.:  
  
 ```  
 if(NOT boost_<module>_FOUND)  
   find_dependency(boost_<module>)  
endif()  
 ```  
   
 This significantly speed up `find_package(boost COMPONENTS <long_list> CONFIG)` calls.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-10-23 14:36:33 UTC  
> Url: https://github.com/boostorg/cmake/issues/40#issuecomment-1775350526  

Fixed by https://github.com/boostorg/cmake/pull/43.
