# #104 CMake 3.8 does not work stand-alone, come forward to non-deprecated 3.10 [Merged]

> Username: nigels-com  
> Created at: 2025-10-31 10:59:19 UTC  
> Updated at: 2025-10-31 13:24:16 UTC  
> Merged at: 2025-10-31 13:24:16 UTC  
> Closed at: 2025-10-31 13:24:16 UTC  
> Url: https://github.com/boostorg/sort/pull/104  

There was a reason to set cmake minimum to 3.10 in PR #99 (later reverted in PR #103)  
  
In stand-alone mode, boost can't be detected.  
Supporting these ancient cmake versions really does seem impractical IMHO.  
With this change, it works fine with cmake 3.10.  
  
```  
$ /opt/cmake-3.8.0-Linux-x86_64/bin/cmake ..  
-- The CXX compiler identification is GNU 13.3.0  
-- Check for working CXX compiler: /usr/bin/c++  
-- Check for working CXX compiler: /usr/bin/c++ -- works  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Using CMake version 3.8.0  
CMake Error at CMakeLists.txt:30 (find_package):  
  Could not find a configuration file for package "Boost" that is compatible  
  with requested version "1.85".  
  
  The following configuration files were considered but not accepted:  
  
    /usr/lib/x86_64-linux-gnu/cmake/Boost-1.83.0/BoostConfig.cmake, version: 1.83.0  
    /lib/x86_64-linux-gnu/cmake/Boost-1.83.0/BoostConfig.cmake, version: 1.83.0  
  
  
  
-- Configuring incomplete, errors occurred!  
See also "/home/nigels/dev/sort/build/CMakeFiles/CMakeOutput.log".  
```

---
