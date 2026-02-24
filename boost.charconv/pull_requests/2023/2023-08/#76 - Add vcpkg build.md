# #76 Add vcpkg build [Merged]

> Username: mborland  
> Created at: 2023-08-17 15:47:09 UTC  
> Updated at: 2023-08-18 12:23:57 UTC  
> Merged at: 2023-08-18 12:23:55 UTC  
> Closed at: 2023-08-18 12:23:55 UTC  
> Url: https://github.com/boostorg/charconv/pull/76  

@cmazakas I generally copied your port file with edits from Microsofts blog. I get the following error:  
  
```  
vcpkg install charconv --overlay-ports=ports/charconv  
  
Computing installation plan...  
The following packages will be built and installed:  
    charconv:arm64-osx -> 1.83.0 -- /Users/mborland/Documents/boost/libs/charconv/ports/charconv  
Detecting compiler hash for triplet arm64-osx...  
Restored 0 package(s) from /Users/mborland/.cache/vcpkg/archives in 5.58 us. Use --debug to see more details.  
Installing 1/1 charconv:arm64-osx...  
Building charconv:arm64-osx...  
warning: -- Using community triplet arm64-osx. This triplet configuration is not guaranteed to succeed.  
-- [COMMUNITY] Loading triplet configuration from: /Users/mborland/vcpkg/triplets/community/arm64-osx.cmake  
-- Installing port from location: /Users/mborland/Documents/boost/libs/charconv/ports/charconv  
-- Using cached cppalliance-charconv-e709ea5705e88faefb1dba9f12108b341f3e44c2.tar.gz.  
-- Cleaning sources at /Users/mborland/vcpkg/buildtrees/charconv/src/341f3e44c2-8c8b7b489c.clean. Use --editable to skip cleaning for the packages you specify.  
-- Extracting source /Users/mborland/vcpkg/downloads/cppalliance-charconv-e709ea5705e88faefb1dba9f12108b341f3e44c2.tar.gz  
-- Using source at /Users/mborland/vcpkg/buildtrees/charconv/src/341f3e44c2-8c8b7b489c.clean  
-- Found external ninja('1.11.1.git.kitware.jobserver-1').  
-- Configuring arm64-osx-dbg  
CMake Error at scripts/cmake/vcpkg_execute_required_process.cmake:112 (message):  
    Command failed: /opt/homebrew/Cellar/cmake/3.27.3/bin/cmake /Users/mborland/vcpkg/buildtrees/charconv/src/341f3e44c2-8c8b7b489c.clean -G Ninja -DCMAKE_BUILD_TYPE=Debug -DCMAKE_INSTALL_PREFIX=/Users/mborland/vcpkg/packages/charconv_arm64-osx/debug -DBUILD_TESTING=OFF -DCMAKE_MAKE_PROGRAM=/opt/homebrew/bin/ninja -DCMAKE_SYSTEM_NAME=Darwin -DBUILD_SHARED_LIBS=OFF -DVCPKG_CHAINLOAD_TOOLCHAIN_FILE=/Users/mborland/vcpkg/scripts/toolchains/osx.cmake -DVCPKG_TARGET_TRIPLET=arm64-osx -DVCPKG_SET_CHARSET_FLAG=ON -DVCPKG_PLATFORM_TOOLSET=external -DCMAKE_EXPORT_NO_PACKAGE_REGISTRY=ON -DCMAKE_FIND_PACKAGE_NO_PACKAGE_REGISTRY=ON -DCMAKE_FIND_PACKAGE_NO_SYSTEM_PACKAGE_REGISTRY=ON -DCMAKE_INSTALL_SYSTEM_RUNTIME_LIBS_SKIP=TRUE -DCMAKE_VERBOSE_MAKEFILE=ON -DVCPKG_APPLOCAL_DEPS=OFF -DCMAKE_TOOLCHAIN_FILE=/Users/mborland/vcpkg/scripts/buildsystems/vcpkg.cmake -DCMAKE_ERROR_ON_ABSOLUTE_INSTALL_DESTINATION=ON -DVCPKG_CXX_FLAGS= -DVCPKG_CXX_FLAGS_RELEASE= -DVCPKG_CXX_FLAGS_DEBUG= -DVCPKG_C_FLAGS= -DVCPKG_C_FLAGS_RELEASE= -DVCPKG_C_FLAGS_DEBUG= -DVCPKG_CRT_LINKAGE=dynamic -DVCPKG_LINKER_FLAGS= -DVCPKG_LINKER_FLAGS_RELEASE= -DVCPKG_LINKER_FLAGS_DEBUG= -DVCPKG_TARGET_ARCHITECTURE=arm64 -DCMAKE_INSTALL_LIBDIR:STRING=lib -DCMAKE_INSTALL_BINDIR:STRING=bin -D_VCPKG_ROOT_DIR=/Users/mborland/vcpkg -DZ_VCPKG_ROOT_DIR=/Users/mborland/vcpkg -D_VCPKG_INSTALLED_DIR=/Users/mborland/vcpkg/installed -DVCPKG_MANIFEST_INSTALL=OFF -DCMAKE_OSX_ARCHITECTURES=arm64  
    Working Directory: /Users/mborland/vcpkg/buildtrees/charconv/arm64-osx-dbg  
    Error code: 1  
    See logs for more information:  
      /Users/mborland/vcpkg/buildtrees/charconv/config-arm64-osx-dbg-CMakeCache.txt.log  
      /Users/mborland/vcpkg/buildtrees/charconv/config-arm64-osx-dbg-out.log  
      /Users/mborland/vcpkg/buildtrees/charconv/config-arm64-osx-dbg-err.log  
  
Call Stack (most recent call first):  
  scripts/cmake/vcpkg_configure_cmake.cmake:326 (vcpkg_execute_required_process)  
  /Users/mborland/Documents/boost/libs/charconv/ports/charconv/portfile.cmake:15 (vcpkg_configure_cmake)  
  scripts/ports.cmake:147 (include)  
  
  
error: building charconv:arm64-osx failed with: BUILD_FAILED  
Elapsed time to handle charconv:arm64-osx: 492 ms  
Please ensure you're using the latest port files with `git pull` and `vcpkg update`.  
Then check for known issues at:  
    https://github.com/microsoft/vcpkg/issues?q=is%3Aissue+is%3Aopen+in%3Atitle+charconv  
You can submit a new issue at:  
    https://github.com/microsoft/vcpkg/issues/new?title=[charconv]+Build+error&body=Copy+issue+body+from+%2FUsers%2Fmborland%2Fvcpkg%2Finstalled%2Fvcpkg%2Fissue_body.md  
```  
  
And the error log contains:  
  
```  
CMake Error at CMakeLists.txt:19 (target_link_libraries):  
  Target "boost_charconv" links to:  
  
    Boost::config  
  
  but the target was not found.  Possible reasons include:  
  
    * There is a typo in the target name.  
    * A find_package call is missing for an IMPORTED target.  
    * An ALIAS target is missing.  
  
  
  
CMake Warning:  
  Manually-specified variables were not used by the project:  
  
    CMAKE_INSTALL_BINDIR  
    CMAKE_INSTALL_LIBDIR  
    VCPKG_CRT_LINKAGE  
    VCPKG_PLATFORM_TOOLSET  
    VCPKG_SET_CHARSET_FLAG  
    _VCPKG_ROOT_DIR  
  
  
CMake Generate step failed.  Build files cannot be regenerated correctly.  
```  
  
Which is the same error you get when you build cmake in the boost/libs/charconv folder instead of the boost/ folder. vcpkg installed the dependencies, but I removed all those from the above log to reduce noise. Any idea how to fix the linking?

---

## Comment 1

> Username: cmazakas  
> Created_at: 2023-08-17 17:36:02 UTC  
> Url: https://github.com/boostorg/charconv/pull/76#issuecomment-1682697784  

Good question.  
  
I was able to build charconv locally using:  
```bash  
exbigboss@DESKTOP-08K7IHD:~/cpp/vcpkg$ ./vcpkg install charconv --overlay-ports=/home/exbigboss/cpp/charconv/ports/  
```  
  
I haven't tested a sample application against this install, however.  
  
Give me a bit more time to look into this issue.

---

## Comment 2

> Username: mborland  
> Created_at: 2023-08-17 17:43:36 UTC  
> Url: https://github.com/boostorg/charconv/pull/76#issuecomment-1682708459  

Thanks. I basically copied the structure from here: https://github.com/microsoft/vcpkg/tree/master/ports/boost-json because it should work.

---

## Comment 3

> Username: cmazakas  
> Created_at: 2023-08-17 21:56:55 UTC  
> Updated_at: 2023-08-17 21:58:15 UTC  
> Url: https://github.com/boostorg/charconv/pull/76#issuecomment-1683031543  

No idea what's going on above.  
  
_But_ I have some great news, Matt, I was able to get vcpkg to build using both "classic" and "manifest" modes.  
  
To make manifest mode, all I did was this:  
```bash  
exbigboss@DESKTOP-08K7IHD:~/cpp/test/__build__$ cat ../CMakeLists.txt  
cmake_minimum_required(VERSION 3.15)  
project(charconv_review CXX)  
  
find_package(Boost REQUIRED COMPONENTS charconv)  
  
add_executable(charconv main.cpp)  
  
target_link_libraries(  
  charconv  
  PRIVATE  
  Boost::charconv  
)  
  
exbigboss@DESKTOP-08K7IHD:~/cpp/test/__build__$ cat ../vcpkg-configuration.json  
{  
  "overlay-ports": ["/home/exbigboss/cpp/charconv/ports"]  
}  
  
exbigboss@DESKTOP-08K7IHD:~/cpp/test/__build__$ cat ../vcpkg.json  
{  
  "dependencies": ["charconv"]  
}  
  
exbigboss@DESKTOP-08K7IHD:~/cpp/test/__build__$ cmake -DCMAKE_TOOLCHAIN_FILE=/home/exbigboss/cpp/vcpkg/scripts/buildsystems/vcpkg.cmake ..  
```  
  
And that was basically it.  
  
Basically, I think this is good enough and easy enough that if we wanted to move forward, we should!

---

## Comment 4

> Username: mborland  
> Created_at: 2023-08-18 12:22:04 UTC  
> Url: https://github.com/boostorg/charconv/pull/76#issuecomment-1683839751  

Perfect. With the addition of https://github.com/cppalliance/charconv/pull/76/commits/d1500fec16abfb377864247f5fe7df5d4e168dd3 I was able to get the library to build as well. You are correct that we should give as many easy ways as possible to consume the library for potential users/reviewers. Thanks for the help.

---
