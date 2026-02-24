# #1000 - Cannot locate rosdep definition for [Boost] [Open]

> Username: yusufersullu  
> Created at: 2025-01-16 13:58:54 UTC  
> Updated at: 2025-01-17 17:00:17 UTC  
> Url: https://github.com/boostorg/boost/issues/1000  

when I run this command:  rosdep install --from-paths . --ignore-src -r -y  
  
this error appears:   
ERROR: the following packages/stacks could not have their rosdep keys resolved  
to system dependencies:  
mesh_rviz_plugins: Cannot locate rosdep definition for [Boost]  
Continuing to install resolvable dependencies...  
#All required rosdeps installed successfully  
  
I don't know how can I solve this

---

## Comment 1

> Username: zaynebaris  
> Created at: 2025-01-16 19:22:05 UTC  
> Url: https://github.com/boostorg/boost/issues/1000#issuecomment-2596632616  

```  
# 1. Install Boost manually  
sudo apt-get install libboost-all-dev  
  
# 2. Update rosdep database  
rosdep update  
  
# 3. Verify or fix the dependency in package.xml open and edit manually if not present  
# Ensure these lines are present in mesh_rviz_plugins/package.xml:  
# <build_depend>boost</build_depend>  
# <exec_depend>boost</exec_depend>  
  
# 4. Run rosdep again  
rosdep install --from-paths . --ignore-src -r -y  
```  
This should address the issue.

---

## Comment 2

> Username: yusufersullu  
> Created at: 2025-01-17 17:00:16 UTC  
> Url: https://github.com/boostorg/boost/issues/1000#issuecomment-2598793845  

Errors     << mesh_rviz_plugins:check /home/yusuf/catkin_ws/logs/mesh_rviz_plugins/build.check.004.log  
CMake Warning (dev) at CMakeLists.txt:2 (project):  
  Policy CMP0048 is not set: project() command manages VERSION variables.  
  Run "cmake --help-policy CMP0048" for policy details.  Use the cmake_policy  
  command to set the policy and suppress this warning.  
  
  The following variable(s) would be set to empty:  
  
    CMAKE_PROJECT_VERSION  
    CMAKE_PROJECT_VERSION_MAJOR  
    CMAKE_PROJECT_VERSION_MINOR  
    CMAKE_PROJECT_VERSION_PATCH  
This warning is for project developers.  Use -Wno-dev to suppress it.  
  
CMake Error at /home/yusuf/catkin_ws/devel/share/catkin_simple/cmake/catkin_simple-extras.cmake:38 (find_package):  
  By not providing "Findboost.cmake" in CMAKE_MODULE_PATH this project has  
  asked CMake to find a package configuration file provided by "boost", but  
  CMake did not find one.  
  
  Could not find a package configuration file provided by "boost" with any of  
  the following names:  
  
    boostConfig.cmake  
    boost-config.cmake  
  
  Add the installation prefix of "boost" to CMAKE_PREFIX_PATH or set  
  "boost_DIR" to a directory containing one of the above files.  If "boost"  
  provides a separate development package or SDK, be sure it has been  
  installed.  
Call Stack (most recent call first):  
  CMakeLists.txt:5 (catkin_simple)  
  
  
make: *** [Makefile:1868: cmake_check_build_system] Error 1  
  
  
  
now this error appears
