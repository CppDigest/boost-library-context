# #28 [CMake] Add test for cmake file [Closed]

> Username: Mike-Devel  
> Created at: 2019-01-05 07:03:11 UTC  
> Updated at: 2019-03-02 16:23:57 UTC  
> Closed at: 2019-03-02 16:23:57 UTC  
> Url: https://github.com/boostorg/pool/pull/28  

Use:   
- create and cd into a build directory (preferably out of tree)  
- `cmake <path-to-boost-pool>/test/test_cmake`  
- `cmake --build . (same as make on linux systems)`  
  
This requires that all dependencies are placed in sibling directories to boost pool (as is e.g. the case when cloning the boost super project)  
  
Unfortunately, I don't know how to integrate it into your travis script.

---
