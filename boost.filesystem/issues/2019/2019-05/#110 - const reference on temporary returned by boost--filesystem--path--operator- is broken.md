# #110 - const reference on temporary returned by boost::filesystem::path::operator/ is broken [Closed]

> Username: MarcoWagner  
> Created at: 2019-05-20 09:29:48 UTC  
> Updated at: 2019-06-19 12:09:49 UTC  
> Closed at: 2019-06-19 12:09:49 UTC  
> Url: https://github.com/boostorg/filesystem/issues/110  

After upgrading from boost 1.69 to boost 1.70 holding a const reference from a temporary returned by ``boost::filesystem::path::operator/`` is broken.  
  
boost-Version: 1.70.0  
Platform: Windows 10 / Version 1803 (Build 17134.765)  
Compiler: Visual Studio 2017 (cl.exe, 19.16.27030.1; link.exe, 14.16.27030)  
  
[main.cpp.txt](https://github.com/boostorg/filesystem/files/3197136/main.cpp.txt)  
[CMakeLists.txt](https://github.com/boostorg/filesystem/files/3197154/CMakeLists.txt)
