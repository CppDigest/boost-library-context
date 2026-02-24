# #243 - CMake Errors [Open]

> Username: Vm900  
> Created at: 2018-11-19 14:10:13 UTC  
> Updated at: 2018-11-19 15:01:58 UTC  
> Url: https://github.com/boostorg/python/issues/243  

Hi,  
I have been trying to compile a program of mine and cmake keeps coughing up the following error:  
Using precompiled header for PythonWrapperGenerator   
Found PythonLibs: /usr/lib/x86_64-linux-gnu/libpython3.6m.so (found suitable version "3.6.7", minimum required is "3")   
CMake Warning at /usr/share/cmake-3.10/Modules/FindBoost.cmake:1626 (message):  
  No header defined for python-py35; skipping header check  
Call Stack (most recent call first):  
  InformationScripting/CMakeLists.txt:12 (find_package)  
  
  
CMake Error at /usr/share/cmake-3.10/Modules/FindBoost.cmake:1947 (message):  
  Unable to find the requested Boost libraries.  
  
  Boost version: 1.65.1  
  
  Boost include path: /usr/include  
  
  Could not find the following Boost libraries:  
  
          boost_python-py35  
  
  No Boost libraries were found.  You may need to set BOOST_LIBRARYDIR to the  
  directory containing Boost libraries or BOOST_ROOT to the location of  
  Boost.  
Call Stack (most recent call first):  
  InformationScripting/CMakeLists.txt:12 (find_package)  
-------------------------------------------------------------------------------------------------------------  
Would appreciate your help.Thanks  
I am on ubuntu btw

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-11-19 15:01:57 UTC  
> Url: https://github.com/boostorg/python/issues/243#issuecomment-439922442  

Sorry, I'm not maintaining any CMake logic to build third-party software with Boost.Python. (In fact, Boost.Python itself isn't built with CMake either.)  
I'd suspect that the issue is related to the naming of the library: We have recently started to include the Python version into the library name itself, so with more recent Boost versions you would use e.g. `-lboost_python36`, etc. It's possible that the CMake logic hasn't caught up to these changes yet.  
But again, I'm not involved in this infrastructure, so this is just a guess.
