# #377 - whether or not  Upstream supports arm-linux [Open]

> Username: zhangbowei  
> Created at: 2021-12-01 09:21:56 UTC  
> Updated at: 2021-12-01 09:21:56 UTC  
> Url: https://github.com/boostorg/python/issues/377  

I use vcpkg to install  boost-python， but failed  
here are some informations.  
  
![image](https://user-images.githubusercontent.com/14348480/144207042-59a20491-1cbb-486e-ad8c-fcf47bcd63f4.png)  
  
  
**command**: `vcpkg install boost-python:arm-linux`  
**vcpkg-scripts version**: 945f540 2021-11-29 (32 hours ago)  
**arm-linux-gnueabihf-gcc --version**:  arm-linux-gnueabihf-gcc (Linaro GCC 7.5-2019.12) 7.5.0  
  
test@ubuntu:~/package$ vcpkg install boost-python:arm-linux  
Computing installation plan...  
The following packages will be built and installed:  
    boost-python[core,python3]:arm-linux -> 1.77.0#1  
Detecting compiler hash for triplet arm-linux...  
Restored 0 packages from /home/test/.cache/vcpkg/archives in 8.319 ms. Use --debug to see more details.  
Starting package 1/1: boost-python:arm-linux  
Building package boost-python[core,python3]:arm-linux...  
-- Using community triplet arm-linux. This triplet configuration is not guaranteed to succeed.  
-- [COMMUNITY] Loading triplet configuration from: /home/test/package/vcpkg/triplets/community/arm-linux.cmake  
-- Using cached boostorg-python-boost-1.77.0.tar.gz.  
-- Cleaning sources at /home/test/package/vcpkg/buildtrees/boost-python/src/ost-1.77.0-c2bb38e3f9.clean. Use --editable to skip cleaning for the packages you specify.  
-- Extracting source /home/test/package/vcpkg/downloads/boostorg-python-boost-1.77.0.tar.gz  
-- Using source at /home/test/package/vcpkg/buildtrees/boost-python/src/ost-1.77.0-c2bb38e3f9.clean  
-- Including /home/test/package/vcpkg/ports/boost-python/b2-options.cmake  
-- Configuring arm-linux-dbg  
-- Configuring arm-linux-rel  
-- Building arm-linux-dbg  
CMake Error at scripts/cmake/vcpkg_execute_build_process.cmake:155 (message):  
    Command failed: /home/test/package/vcpkg/downloads/tools/cmake-3.21.1-linux/cmake-3.21.1-linux-x86_64/bin/cmake --build . --config Debug --target install -- -v -j2  
    Working Directory: /home/test/package/vcpkg/buildtrees/boost-python/arm-linux-dbg  
    See logs for more information:  
      /home/test/package/vcpkg/buildtrees/boost-python/install-arm-linux-dbg-out.log  
  
Call Stack (most recent call first):  
  installed/x64-linux/share/vcpkg-cmake/vcpkg_cmake_build.cmake:102 (vcpkg_execute_build_process)  
  installed/x64-linux/share/vcpkg-cmake/vcpkg_cmake_install.cmake:41 (vcpkg_cmake_build)  
  installed/x64-linux/share/boost-build/boost-modular-build.cmake:105 (vcpkg_cmake_install)  
  ports/boost-python/portfile.cmake:15 (boost_modular_build)  
  scripts/ports.cmake:142 (include)  
  
  
Error: Building package boost-python:arm-linux failed with: BUILD_FAILED  
Please ensure you're using the latest portfiles with `git pull` and `./vcpkg update`.  
Then check for known issues at:  
  https://github.com/microsoft/vcpkg/issues?q=is%3Aissue+is%3Aopen+in%3Atitle+boost-python  
You can submit a new issue at:  
  https://github.com/microsoft/vcpkg/issues/new?template=report-package-build-failure.md&title=[boost-python]+Build+error  
including:  
  package: boost-python[core,python3]:arm-linux -> 1.77.0#1  
    vcpkg-tool version: 2021-11-24-48b94a6946b8a70abd21529218927fd478d02b6c  
    vcpkg-scripts version: 945f540 2021-11-29 (32 hours ago)  
  
Additionally, attach any relevant sections from the log files above.
