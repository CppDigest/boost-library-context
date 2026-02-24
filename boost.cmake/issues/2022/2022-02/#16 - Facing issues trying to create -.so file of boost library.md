# #16 - Facing issues trying to create *.so file of boost library [Closed]

> Username: nitolpalak  
> Created at: 2022-02-02 04:35:00 UTC  
> Updated at: 2022-02-07 14:41:01 UTC  
> Closed at: 2022-02-07 14:41:01 UTC  
> Url: https://github.com/boostorg/cmake/issues/16  

OS: Ubuntu 20.04  
  
I am facing issues while trying to build Boost library and create *.so file. I have cloned the project, created a `build/` folder and run `cmake ../` from the build folder. The output is as following:  
```  
-- The CXX compiler identification is GNU 9.3.0  
-- Check for working CXX compiler: /usr/bin/c++  
-- Check for working CXX compiler: /usr/bin/c++ -- works  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
CMake Error at CMakeLists.txt:20 (include):  
  include could not find load file:  
  
    BoostRoot  
  
  
-- Configuring incomplete, errors occurred!  
See also "/home/niaz/boost/build/CMakeFiles/CMakeOutput.log".  
  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2022-02-02 04:41:55 UTC  
> Url: https://github.com/boostorg/cmake/issues/16#issuecomment-1027571839  

You need to perform a recursive clone in order to clone the submodules.

---

## Comment 2

> Username: nitolpalak  
> Created at: 2022-02-07 09:43:57 UTC  
> Url: https://github.com/boostorg/cmake/issues/16#issuecomment-1031264326  

Thanks it worked
