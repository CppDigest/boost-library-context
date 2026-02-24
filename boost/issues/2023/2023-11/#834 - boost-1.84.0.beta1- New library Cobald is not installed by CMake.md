# #834 - boost-1.84.0.beta1: New library Cobald is not installed by CMake [Closed]

> Username: bebuch  
> Created at: 2023-11-28 16:16:22 UTC  
> Updated at: 2023-11-29 10:18:53 UTC  
> Closed at: 2023-11-29 10:18:53 UTC  
> Url: https://github.com/boostorg/boost/issues/834  

When I configure, build and install via CMake then Cobald is missing in the installed files. The other new library redis is installed correctly.  
  
I downloaded from: https://github.com/boostorg/boost/releases/download/boost-1.84.0.beta1/boost-1.84.0.beta1.7z

---

## Comment 1

> Username: pdimov  
> Created at: 2023-11-29 06:03:48 UTC  
> Url: https://github.com/boostorg/boost/issues/834#issuecomment-1831273329  

Using what compiler and what platform?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-11-29 06:10:12 UTC  
> Url: https://github.com/boostorg/boost/issues/834#issuecomment-1831280619  

Which c++ standard did you compile with?

---

## Comment 3

> Username: bebuch  
> Created at: 2023-11-29 07:25:25 UTC  
> Url: https://github.com/boostorg/boost/issues/834#issuecomment-1831351138  

C++20, I can post the full command line calls later if this helps.  
  
Klemens Morgenstern ***@***.***> schrieb am Mi., 29. Nov.  
2023, 07:10:  
  
> Which c++ standard did you compile with?  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/boost/issues/834#issuecomment-1831280619>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AC3UCSIEQOUADWC4ZVQ6LW3YG3GU7AVCNFSM6AAAAAA76A5XEWVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMYTQMZRGI4DANRRHE>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>

---

## Comment 4

> Username: bebuch  
> Created at: 2023-11-29 10:18:53 UTC  
> Url: https://github.com/boostorg/boost/issues/834#issuecomment-1831607304  

I found the problem:  
  
```  
-- Boost.Cobalt: not building, the lowest supported MSVC version is 1930.  1929 is not supported  
```  
  
I use a VS 2019 x64 cmd command line. Directory structure:  
  
```  
C:/Temp/boost-1.84.0.beta1  
C:/Temp/boost-build  
C:/Temp/boost-install  
```  
  
```batch  
cd C:/Temp/boost-1.84.0.beta1  
cmake "-GNinja" "-DCMAKE_CXX_STANDARD=20" "-DCMAKE_INSTALL_PREFIX=C:\Temp\boost-install" "C:\Temp\boost-1.84.0.beta1"  
cmake --build .  
cmake --install .  
```
