# #494 - Is there a corresponding version relationship between boost.python and Python 3 [Open]

> Username: excellentcamera  
> Created at: 2025-06-23 06:09:21 UTC  
> Updated at: 2025-06-23 07:59:15 UTC  
> Url: https://github.com/boostorg/python/issues/494  

I'm compiling the omplapp1.7.0 library. The compilation is successful, but no GUI animation window pops up when running any instance program. The query deepseek indicates that the python binding may not be successful because D:\QJ\omplapp-1.7.0\build\lib\Release\ompl*.pyd is not generated in the local directory. So when returning to the cmake-gui software, it was found that there was such an alarm:  
  
CMake Warning at D: / SoftWare/Cmake4.0.0 / CMake - 4.0.0 - Windows - x86_64 / share/CMake - 4.0 / Modules/FindBoost. CMake: 1427 (message):  
        **New Boost version** may have incorrect or missing dependencies and imported targets  
Call Stack (most recent call first):  
      D: / SoftWare/Cmake4.0.0 / cmake - 4.0.0 - Windows - x86_64 / share/cmake - 4.0 / Modules/FindBoost. Cmake: 1552   
      (_Boost_COMPONENT_DEPENDENCIES)  
      D: / SoftWare/Cmake4.0.0 / cmake - 4.0.0 - Windows - x86_64 / share/cmake - 4.0 / Modules/FindBoost. Cmake: 2164   
      (_Boost_MISSING_DEPENDENCIES)  
      CMakeLists.txt:65 (find_package)  
  
CMake Warning at D: / SoftWare/Cmake4.0.0 / CMake - 4.0.0 - Windows - x86_64 / share/CMake - 4.0 / Modules/FindBoost. CMake: 1427 (message):  
      **New Boost version** may have incorrect or missing dependencies and imported targets  
Call Stack (most recent call first):  
      D: / SoftWare/Cmake4.0.0 / cmake - 4.0.0 - Windows - x86_64 / share/cmake - 4.0 / Modules/FindBoost. Cmake: 1552   
      (_Boost_COMPONENT_DEPENDENCIES)  
      D: / SoftWare/Cmake4.0.0 / cmake - 4.0.0 - Windows - x86_64 / share/cmake - 4.0 / Modules/FindBoost. Cmake: 2164   
      (_Boost_MISSING_DEPENDENCIES)  
      ompl/CMakeModules/FindPython.cmake:189 (find_package)  
      CMakeLists.txt:95 (find_boost_python).  
  
By the way, I installed boost:x64-windows 1.88.0 and python3.12.9 using vcpkg.exe. Is there a version correspondence between them

---

## Comment 1

> Username: CEXT-Dan  
> Created at: 2025-06-23 07:59:15 UTC  
> Url: https://github.com/boostorg/python/issues/494#issuecomment-2995355335  

The latest version on [source forge (1.88)](https://sourceforge.net/projects/boost/files/boost/) is linked with Python312. That’s the combo I use on [my project]( https://github.com/CEXT-Dan/PyRx) . I’ve never tried to compile boost myself
