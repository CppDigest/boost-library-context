# #594 - Boost 1.78 Fiber and Stacktrace libs not installed [Closed]

> Username: gpmiller11  
> Created at: 2021-12-13 06:09:49 UTC  
> Updated at: 2022-07-31 01:09:12 UTC  
> Closed at: 2022-07-31 01:09:12 UTC  
> Url: https://github.com/boostorg/boost/issues/594  

Hello,  
  
I'm a relatively new user of Boost, so please let me know if I haven't provided sufficient info or if this issue belongs elsewhere. I've been attempting to build Boost 1.78 locally (Ubuntu 20.04, WSL) from the tar download:  
```  
[boost]$ sha256sum boost_1_78_0.tar.gz  
94ced8b72956591c4775ae2207a9763d3600b30d9d7446562c552f0a14a63be7  boost_1_78_0.tar.gz  
```  
  
Using  
```  
[boost]$ gcc --version  
gcc (Ubuntu 9.3.0-17ubuntu1~20.04) 9.3.0  
```  
  
I noticed that 4 libs appeared to be missing when I tried to build everything ('libboost_fiber`, `libboost_stacktrace_noop`, `libboost_stacktrace_addr2line`, and `libboost_stacktrace_basic`). As a smaller reproduction, when I execute the following (atomic as a "working" example plus the ones I found to be missing):  
```  
[boost]$  tar -xvf boost_1_78_0.tar.gz && cd boost_1_78_0 && ./bootstrap.sh --with-toolset=gcc --with-libraries=atomic,fiber,stacktrace && ./b2 -j8 cxxflags=-std=c++17  
```  
Then `./stage/lib` only appears to get the `atomic` libs:  
```  
[boost_1_78_0]$ ls ./stage/lib  
cmake  libboost_atomic.a  libboost_atomic.so  libboost_atomic.so.1.78.0  
```  
  
As a sanity check, I also tried a comparable command on `boost_1_77_0.tar.gz` and found the `fiber` and `stacktrace` libs to be available in `./stage/lib` as expected post build.  
  
Am I doing anything incorrectly here / is this a known issue?

---

## Comment 1

> Username: gpmiller11  
> Created at: 2021-12-13 06:10:21 UTC  
> Url: https://github.com/boostorg/boost/issues/594#issuecomment-992140178  

Edit: maybe related to https://github.com/boostorg/fiber/issues/286 but didn't see any sort of resolution there really

---

## Comment 2

> Username: olk  
> Created at: 2021-12-18 08:25:08 UTC  
> Updated at: 2021-12-18 08:32:29 UTC  
> Url: https://github.com/boostorg/boost/issues/594#issuecomment-997169005  

I believe this issue is related to the cmake stuff.  
`b2 --with-fiber`  creates the fiber library under directory `bin.v2/libs/fiber/build/gcc-11/release/threading-multi/visibility-hidden/libboost_fiber.so.1.78.0`.

---

## Comment 3

> Username: moretromain  
> Created at: 2022-01-02 22:51:26 UTC  
> Updated at: 2022-01-03 01:19:09 UTC  
> Url: https://github.com/boostorg/boost/issues/594#issuecomment-1003787686  

I also reproduce this issue with windows/msvc (14.29): `b2 --with-fiber` outputs static libs in `\bin.v2\libs\fiber\build\msvc-14.2\release\link-static\threading-multi\libboost_fiber-vc142-mt-x64-1_78.lib` and `bin.v2\libs\fiber\build\msvc-14.2\debug\link-static\threading-multi\libboost_fiber-vc142-mt-gd-x64-1_78.lib` but the libraries are not installed into `stage\lib` as expected.  
  
I tried different variants and build options (link=static/shared and various debug/release/runtime-link combos), but boost.fiber is the only library that exhibits this behavior, no matter the build configuration (I didn't check StackTrace though).  
  
The issue does not occur with 1.77.0 when using the exact same build commands.  
  
I tried to dig into the cmake stuff to see if I could find something obvious there that could explain the differences between libs, but after quite some time I couldn't find anything. I'm not familiar enough with boost.build to effectively debug this issue, but I'd be happy to help if someone could point me into the right direction, as I already spent quite some time trying to update my codebase to boost 1.78.   
  
  
Quick Update: I managed to get the libraries copied into the stage folder by replacing `boost-install boost_fiber boost_fiber_numa ;` with `boost-install boost_fiber ;` (removing the numa library) in `libs\fiber\build\Jamfile.v2`. Something in boost-install doesn't like the two libraries as arguments when one is conditionally enabled, I'll try to find what it is.  
Using `b2 --with-fiber numa=on` works by default on the other hand without modifying anything, so this is definitely related to the conditional stuff.

---

## Comment 4

> Username: brad0  
> Created at: 2022-03-18 01:17:48 UTC  
> Url: https://github.com/boostorg/boost/issues/594#issuecomment-1071927144  

This is probably related to the  known issues listed item..  
  
https://www.boost.org/patches/1_78_0/0001-b2-fix-install.patch

---

## Comment 5

> Username: mclow  
> Created at: 2022-03-23 19:15:01 UTC  
> Url: https://github.com/boostorg/boost/issues/594#issuecomment-1076727234  

Can you try the 1.79.0 beta and confirm that this is fixed there?  https://boostorg.jfrog.io/artifactory/main/beta/1.79.0.beta1/source/

---

## Comment 6

> Username: brad0  
> Created at: 2022-03-23 19:24:18 UTC  
> Url: https://github.com/boostorg/boost/issues/594#issuecomment-1076734708  

> Can you try the 1.79.0 beta and confirm that this is fixed there? https://boostorg.jfrog.io/artifactory/main/beta/1.79.0.beta1/source/  
  
The errata patch that went out for 1.78.0 has not been integrated into a release and pushed to the 1.79 release cycle until after beta1 went out. The fix should be in the next 1.79 release.
