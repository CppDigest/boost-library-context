# #9 - vcpkg install of boost-uuid generates error at the end [Closed]

> Username: al-sabr  
> Created at: 2021-04-22 17:39:53 UTC  
> Updated at: 2021-04-22 20:28:29 UTC  
> Closed at: 2021-04-22 20:28:29 UTC  
> Url: https://github.com/boostorg/cmake/issues/9  

```  
  
Installing package boost-spirit\[core\]:x64-windows...  
  
Installing package boost-spirit\[core\]:x64-windows... done  
  
Elapsed time for package boost-spirit:x64-windows: 20.11 s  
  
Starting package 15/17: boost-serialization:x64-windows  
  
Building package boost-serialization\[core\]:x64-windows...  
  
\-- Downloading https://github.com/boostorg/serialization/archive/boost-1.75.0.tar.gz -> boostorg-serialization-boost-1.75.0.tar.gz...  
  
\-- Downloading https://github.com/boostorg/serialization/archive/boost-1.75.0.tar.gz... Failed. Status: 35;"SSL connect error"  
  
CMake Error at scripts/cmake/vcpkg\_download\_distfile.cmake:184 (message):  
  
     Failed to download file.  
  
     If you use a proxy, please set the HTTPS\_PROXY and HTTP\_PROXY environment  
  
     variables to "https://user:password@your-proxy-ip-address:port/".  
  
     If error with status 4 (Issue #15434),  
  
     try setting "http://user:password@your-proxy-ip-address:port/".  
  
     Otherwise, please submit an issue at https://github.com/Microsoft/vcpkg/issues  
  
Call Stack (most recent call first):  
  
 scripts/cmake/vcpkg\_from\_github.cmake:147 (vcpkg\_download\_distfile)  
  
 ports/boost-serialization/portfile.cmake:3 (vcpkg\_from\_github)  
  
 scripts/ports.cmake:142 (include)  
  
Error: Building package boost-serialization:x64-windows failed with: BUILD\_FAILED  
  
Please ensure you're using the latest portfiles with \`.\\vcpkg update\`, then  
  
submit an issue at https://github.com/Microsoft/vcpkg/issues including:  
  
 Package: boost-serialization:x64-windows  
  
 Vcpkg version: 2021-01-13-d67989bce1043b98092ac45996a8230a059a2d7e  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2021-04-22 20:28:29 UTC  
> Url: https://github.com/boostorg/cmake/issues/9#issuecomment-825163017  

@mclow This issue has nothing to do with this repository.  
  
@al-sabr As the error message says,  
```  
Downloading https://github.com/boostorg/serialization/archive/boost-1.75.0.tar.gz... Failed. Status: 35;"SSL connect error"  
```  
this failure is caused by inability to download `https://github.com/boostorg/serialization/archive/boost-1.75.0.tar.gz`. This is neither a Boost issue nor a Vcpkg issue. It's an issue with your internet connection.
