# #433 - Is Boost.Python is bound to specific python version? [Closed]

> Username: fralik  
> Created at: 2024-04-22 08:44:41 UTC  
> Updated at: 2024-05-02 06:48:27 UTC  
> Closed at: 2024-05-02 06:48:27 UTC  
> Url: https://github.com/boostorg/python/issues/433  

I am trying to understand if Boost.Python has binding for a particular version of Python? I am using Cmake+vcpkg, if I do  
```  
find_package(Python COMPONENTS Interpreter Development.Module Development.SABIModule REQUIRED)  
find_package(Boost REQUIRED COMPONENTS python${Python_VERSION_MAJOR}${Python_VERSION_MINOR})  
```  
and my vcpkg is installing the latest versions of boost-python (1.8.4), then everything works if I have python 3.11. But if I have a different python version, then boost complains that it can't find Python:  
```  
-- Found Python: C:\Users\user\AppData\Local\Temp\build-env-tf4blxq2\Scripts\python.exe (found version "3.9.19") found components: Interpreter Development.Module Development.SABIModule  
-- Python_INCLUDE_DIRS: C:/Users/user/miniconda3/envs/conda-env-build-39/Include  
-- Python_LIBRARIES: C:/Users/user/miniconda3/envs/conda-env-build-39/libs/python39.lib  
-- Python3_VERSION_MAJOR: 3  
-- Python3_VERSION_MINOR: 9  
CMake Error at C:/Program Files/CMake/share/cmake-3.29/Modules/FindPackageHandleStandardArgs.cmake:230 (message):  
  Could NOT find Boost (missing: python39) (found version "1.84.0")  
Call Stack (most recent call first):  
  C:/Program Files/CMake/share/cmake-3.29/Modules/FindPackageHandleStandardArgs.cmake:600 (_FPHSA_FAILURE_MESSAGE)  
  C:/Program Files/CMake/share/cmake-3.29/Modules/FindBoost.cmake:2393 (find_package_handle_standard_args)  
  C:/repos/pkg4/build/vcpkg_installed/x64-windows/share/boost/vcpkg-cmake-wrapper.cmake:11 (_find_package)  
  C:/repos/vcpkg/scripts/buildsystems/vcpkg.cmake:813 (include)  
  CMakeLists.txt:45 (find_package)  
```  
I wonder if this is related to Boost.Python or perhaps this is vcpkg issue, or something else?

---

## Comment 1

> Username: nega0  
> Created at: 2024-05-02 04:33:49 UTC  
> Url: https://github.com/boostorg/python/issues/433#issuecomment-2089558091  

`boost::python` is bound to the version of Python it was built against.
