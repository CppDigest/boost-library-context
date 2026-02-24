# #933 - Visual Studio solution generation fails [Closed]

> Username: vinniefalco  
> Created at: 2025-10-20 02:49:05 UTC  
> Updated at: 2025-10-22 16:05:48 UTC  
> Closed at: 2025-10-22 16:05:48 UTC  
> Url: https://github.com/boostorg/url/issues/933  

Command line:  
```  
cmake -G "Visual Studio 17 2022" -A x64 -B bin64 -DCMAKE_TOOLCHAIN_FILE=C:/vcpkg/scripts/buildsystems/vcpkg.cmake -DVCPKG_CHAINLOAD_TOOLCHAIN_FILE="C:/Users/vinnie/src/toolchains/msvc.cmake"  
```  
  
<img width="1250" height="482" alt="Image" src="https://github.com/user-attachments/assets/dda0b3d3-f1bb-4bd1-a6be-b2de79eae7aa" />
