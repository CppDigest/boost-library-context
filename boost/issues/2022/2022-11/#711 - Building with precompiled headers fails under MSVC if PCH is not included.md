# #711 - Building with precompiled headers fails under MSVC if PCH is not included [Closed]

> Username: anarthal  
> Created at: 2022-11-15 20:45:47 UTC  
> Updated at: 2024-03-27 15:02:52 UTC  
> Closed at: 2024-03-27 15:02:51 UTC  
> Url: https://github.com/boostorg/boost/issues/711  

.\b2.exe --version: B2 4.9-git  
OS: Windows 10  
toolset: msvc-14.3  
Running in a Boost 1.80.0, as per cloning from boostorg/boost, initializing submodules and .\bootstrap.bat.  
  
File structure:  
    libs/atest/Jamfile  
    libs/atest/acpp.cpp  
    libs/atest/apch.hpp  
  
Content in https://gist.github.com/anarthal/221c0ee6f3d2640fc6bba892102cf3dd  
  
Running with .\b2 .\libs\atest//anexe  
  
Fails with  
  
Performing configuration checks  
  
    - default address-model    : 64-bit [1]  
    - default architecture     : x86 [1]  
    - symlinks supported       : no  
    - junctions supported      : yes  
    - hardlinks supported      : yes  
  
[1] msvc-14.3  
...found 14 targets...    
...updating 9 targets...  
compile-c-c++-pch bin.v2\libs\atest\msvc-14.3\debug\threading-multi\apch.pch  
apch.pch.cpp  
compile-c-c++ bin.v2\libs\atest\msvc-14.3\debug\threading-multi\acpp.obj  
acpp.cpp  
libs\atest\acpp.cpp(2): fatal error C1010: unexpected end of file while looking for precompiled header. Did you forget to add '#include "apch.hpp"' to your source?  
  
    call "bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo "libs\atest\acpp.cpp" -c -Fo"bin.v2\libs\atest\msvc-14.3\debug\threading-multi\acpp.obj"  -FI"libs\atest\apch.hpp" -Yu"apch.hpp" -Fp"bin.v2\libs\atest\msvc-14.3\debug\threading-multi\apch.pch" -TP /wd4675 /EHs /GR /Zc:throwingNew /Z7 /Od /Ob0 /W3 /MDd /Zc:forScope /Zc:wchar_t /Zc:inline /favor:blend -DBOOST_ALL_NO_LIB=1 -DBOOST_BUILD_PCH_ENABLED "-I."  
  
...failed compile-c-c++ bin.v2\libs\atest\msvc-14.3\debug\threading-multi\acpp.obj...  
...skipped <pbin.v2\libs\atest\msvc-14.3\debug\threading-multi>anexe.exe for lack of <pbin.v2\libs\atest\msvc-14.3\debug\threading-multi>acpp.obj...  
...skipped <pbin.v2\libs\atest\msvc-14.3\debug\threading-multi>anexe.pdb for lack of <pbin.v2\libs\atest\msvc-14.3\debug\threading-multi>acpp.obj...  
...failed updating 1 target...  
...skipped 2 targets...  
...updated 6 targets...

---

## Comment 1

> Username: mclow  
> Created at: 2022-11-15 21:40:24 UTC  
> Url: https://github.com/boostorg/boost/issues/711#issuecomment-1315897434  

I would appreciate it if you would try this with the Boost 1.81.0 beta release candidate, and let us know if it still fails.  
  
https://boostorg.jfrog.io/artifactory/main/beta/1.81.0.beta1/source/

---

## Comment 2

> Username: anarthal  
> Created at: 2022-11-17 17:12:36 UTC  
> Url: https://github.com/boostorg/boost/issues/711#issuecomment-1318950576  

Fails with the same output.

---

## Comment 3

> Username: anarthal  
> Created at: 2024-03-27 15:02:51 UTC  
> Url: https://github.com/boostorg/boost/issues/711#issuecomment-2023002413  

I can confirm this is now fixed.
