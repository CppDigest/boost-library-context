# #885 - I failed to change the boost library version.What should I do? [Open]

> Username: whsm  
> Created at: 2024-04-12 03:58:13 UTC  
> Updated at: 2024-04-12 03:58:13 UTC  
> Url: https://github.com/boostorg/boost/issues/885  

I'm sorry to bother you.In order to use GUDHI, I installed boost c++ libraries, according to the suggestion of URL "https://gudhi.inria.fr/doc/latest/installation.html". I installed boost 1.84.0, 1.71.0, and 1.74.0 successively. Due to the mismatch, the folders of these three versions were directly deleted. Then boost 1.80.0 was installed. To generate the utility GUDHI, I run the command "cmake -DCMAKE_BUILD_TYPE=Release ..". The error result is as follows.What should I do?My operating system is Win11. My compiler is Visual Studio2022.Thank you for your help.  
  
  
  
  
  
PS D:\Program Files (x86)\gudhi-devel\gudhi-devel> cd build/  
PS D:\Program Files (x86)\gudhi-devel\gudhi-devel\build> cmake -DCMAKE_BUILD_TYPE=Release ..  
-- Selecting Windows SDK version 10.0.22621.0 to target Windows 10.0.22631.  
-- GUDHI version : 3.10.0a1  
CMake Error at D:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.28/Modules/FindBoost.cmake:1841 (file):  
  file STRINGS file "D:/Program Files  
  (x86)/boost_1_84_0/boost_1_84_0/boost/version.hpp" cannot be read.  
Call Stack (most recent call first):  
  src/cmake/modules/GUDHI_third_party_libraries.cmake:3 (find_package)  
  CMakeLists.txt:20 (include)  
  
  
CMake Warning at D:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.28/Modules/FindBoost.cmake:1015 (message):  
  Imported targets and dependency information not available for Boost version  
  0.0.0 (all versions older than 1.33)  
Call Stack (most recent call first):  
  D:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.28/Modules/FindBoost.cmake:1523 (_Boost_COMPONENT_DEPENDENCIES)  
  D:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.28/Modules/FindBoost.cmake:2134 (_Boost_MISSING_DEPENDENCIES)  
  src/cmake/modules/GUDHI_third_party_libraries.cmake:3 (find_package)  
  CMakeLists.txt:20 (include)  
  
  
CMake Warning at D:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.28/Modules/FindBoost.cmake:1015 (message):  
  Imported targets and dependency information not available for Boost version  
  0.0.0 (all versions older than 1.33)  
Call Stack (most recent call first):  
  D:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.28/Modules/FindBoost.cmake:1523 (_Boost_COMPONENT_DEPENDENCIES)  
  D:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.28/Modules/FindBoost.cmake:2134 (_Boost_MISSING_DEPENDENCIES)  
  src/cmake/modules/GUDHI_third_party_libraries.cmake:3 (find_package)  
  CMakeLists.txt:20 (include)  
  
  
CMake Warning at D:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.28/Modules/FindBoost.cmake:1015 (message):  
  Imported targets and dependency information not available for Boost version  
  0.0.0 (all versions older than 1.33)  
Call Stack (most recent call first):  
  D:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.28/Modules/FindBoost.cmake:1523 (_Boost_COMPONENT_DEPENDENCIES)  
  D:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.28/Modules/FindBoost.cmake:2134 (_Boost_MISSING_DEPENDENCIES)  
  src/cmake/modules/GUDHI_third_party_libraries.cmake:3 (find_package)  
  CMakeLists.txt:20 (include)  
  
  
CMake Error at src/cmake/modules/GUDHI_third_party_libraries.cmake:7 (message):  
  NOTICE: This program requires Boost and will not be compiled.  
Call Stack (most recent call first):  
  CMakeLists.txt:20 (include)  
  
  
-- Configuring incomplete, errors occurred!  
PS D:\Program Files (x86)\gudhi-devel\gudhi-devel\build>  
![屏幕截图 2024-04-12 113041](https://github.com/boostorg/boost/assets/33314971/664e60dc-8e4e-47b5-9950-21c1b24c2e23)
