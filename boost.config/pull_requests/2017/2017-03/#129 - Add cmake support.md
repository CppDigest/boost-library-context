# #129 Add cmake support [Closed]

> Username: pfultz2  
> Created at: 2017-03-24 15:24:40 UTC  
> Updated at: 2018-08-06 11:15:25 UTC  
> Closed at: 2018-08-06 11:15:25 UTC  
> Url: https://github.com/boostorg/config/pull/129  

This adds cmake support for building, testing, and installing Boost.Config. This requires first installing [BCM](https://github.com/boost-cmake/bcm). To build, test, and install Boost.Config, just run:  
  
```  
mkdir build  
cd build  
cmake ..  
cmake --build . --target  
cmake --build . --target check  
cmake --build . --target install  
```  
  
This also adds travis and appveyor which will test the library on every push.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-08-06 11:15:25 UTC  
> Url: https://github.com/boostorg/config/pull/129#issuecomment-410674652  

Closing as obsolete - Config now has it's own CI, and there is a Boost-wide CMake effort.

---
