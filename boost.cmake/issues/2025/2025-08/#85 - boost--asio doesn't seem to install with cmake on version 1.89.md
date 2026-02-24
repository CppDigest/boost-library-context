# #85 - boost::asio doesn't seem to install with cmake on version 1.89 [Open]

> Username: miyanyan  
> Created at: 2025-08-17 04:36:55 UTC  
> Updated at: 2025-10-20 15:09:05 UTC  
> Url: https://github.com/boostorg/cmake/issues/85  

Seems the same error as https://github.com/boostorg/cmake/issues/84  
  
This error will occur when there are multi library targets?,  https://github.com/boostorg/asio/commit/be4af2d923f6145cd384e9bd1d572a5f204f5461  
  
```  
[1/1] "D:/github/miyanyan/vcpkg/downloads/tools/cmake-3.30.1-windows/cmake-3.30.1-windows-i386/bin/cmake.exe" -E chdir ".." "D:/github/miyanyan/vcpkg/downloads/tools/cmake-3.30.1-windows/cmake-3.30.1-windows-i386/bin/cmake.exe" "D:/github/miyanyan/vcpkg/buildtrees/boost-asio/src/ost-1.89.0-dd9ecc779b.clean" "-G" "Ninja" "-DCMAKE_BUILD_TYPE=Release" "-DCMAKE_INSTALL_PREFIX=D:/github/miyanyan/vcpkg/packages/boost-asio_x64-windows" "-DFETCHCONTENT_FULLY_DISCONNECTED=ON" "-DBOOST_INCLUDE_LIBRARIES=asio" "-DBOOST_RUNTIME_LINK=dynamic" "-DBOOST_INSTALL_INCLUDE_SUBDIR=" "-DCMAKE_MSVC_DEBUG_INFORMATION_FORMAT=" "-DCMAKE_MAKE_PROGRAM=C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/Ninja/ninja.exe" "-DBUILD_SHARED_LIBS=ON" "-DVCPKG_CHAINLOAD_TOOLCHAIN_FILE=D:/github/miyanyan/vcpkg/scripts/toolchains/windows.cmake" "-DVCPKG_TARGET_TRIPLET=x64-windows" "-DVCPKG_SET_CHARSET_FLAG=ON" "-DVCPKG_PLATFORM_TOOLSET=v143" "-DCMAKE_EXPORT_NO_PACKAGE_REGISTRY=ON" "-DCMAKE_FIND_PACKAGE_NO_PACKAGE_REGISTRY=ON" "-DCMAKE_FIND_PACKAGE_NO_SYSTEM_PACKAGE_REGISTRY=ON" "-DCMAKE_INSTALL_SYSTEM_RUNTIME_LIBS_SKIP=TRUE" "-DCMAKE_VERBOSE_MAKEFILE=ON" "-DVCPKG_APPLOCAL_DEPS=OFF" "-DCMAKE_TOOLCHAIN_FILE=D:/github/miyanyan/vcpkg/scripts/buildsystems/vcpkg.cmake" "-DCMAKE_ERROR_ON_ABSOLUTE_INSTALL_DESTINATION=ON" "-DVCPKG_CXX_FLAGS=" "-DVCPKG_CXX_FLAGS_RELEASE=" "-DVCPKG_CXX_FLAGS_DEBUG=" "-DVCPKG_C_FLAGS=" "-DVCPKG_C_FLAGS_RELEASE=" "-DVCPKG_C_FLAGS_DEBUG=" "-DVCPKG_CRT_LINKAGE=dynamic" "-DVCPKG_LINKER_FLAGS=" "-DVCPKG_LINKER_FLAGS_RELEASE=" "-DVCPKG_LINKER_FLAGS_DEBUG=" "-DVCPKG_TARGET_ARCHITECTURE=x64" "-DCMAKE_INSTALL_LIBDIR:STRING=lib" "-DCMAKE_INSTALL_BINDIR:STRING=bin" "-D_VCPKG_ROOT_DIR=D:/github/miyanyan/vcpkg" "-D_VCPKG_INSTALLED_DIR=D:/github/miyanyan/vcpkg/installed" "-DVCPKG_MANIFEST_INSTALL=OFF"  
FAILED: ../CMakeCache.txt   
"D:/github/miyanyan/vcpkg/downloads/tools/cmake-3.30.1-windows/cmake-3.30.1-windows-i386/bin/cmake.exe" -E chdir ".." "D:/github/miyanyan/vcpkg/downloads/tools/cmake-3.30.1-windows/cmake-3.30.1-windows-i386/bin/cmake.exe" "D:/github/miyanyan/vcpkg/buildtrees/boost-asio/src/ost-1.89.0-dd9ecc779b.clean" "-G" "Ninja" "-DCMAKE_BUILD_TYPE=Release" "-DCMAKE_INSTALL_PREFIX=D:/github/miyanyan/vcpkg/packages/boost-asio_x64-windows" "-DFETCHCONTENT_FULLY_DISCONNECTED=ON" "-DBOOST_INCLUDE_LIBRARIES=asio" "-DBOOST_RUNTIME_LINK=dynamic" "-DBOOST_INSTALL_INCLUDE_SUBDIR=" "-DCMAKE_MSVC_DEBUG_INFORMATION_FORMAT=" "-DCMAKE_MAKE_PROGRAM=C:/Program Files/Microsoft Visual Studio/2022/Community/Common7/IDE/CommonExtensions/Microsoft/CMake/Ninja/ninja.exe" "-DBUILD_SHARED_LIBS=ON" "-DVCPKG_CHAINLOAD_TOOLCHAIN_FILE=D:/github/miyanyan/vcpkg/scripts/toolchains/windows.cmake" "-DVCPKG_TARGET_TRIPLET=x64-windows" "-DVCPKG_SET_CHARSET_FLAG=ON" "-DVCPKG_PLATFORM_TOOLSET=v143" "-DCMAKE_EXPORT_NO_PACKAGE_REGISTRY=ON" "-DCMAKE_FIND_PACKAGE_NO_PACKAGE_REGISTRY=ON" "-DCMAKE_FIND_PACKAGE_NO_SYSTEM_PACKAGE_REGISTRY=ON" "-DCMAKE_INSTALL_SYSTEM_RUNTIME_LIBS_SKIP=TRUE" "-DCMAKE_VERBOSE_MAKEFILE=ON" "-DVCPKG_APPLOCAL_DEPS=OFF" "-DCMAKE_TOOLCHAIN_FILE=D:/github/miyanyan/vcpkg/scripts/buildsystems/vcpkg.cmake" "-DCMAKE_ERROR_ON_ABSOLUTE_INSTALL_DESTINATION=ON" "-DVCPKG_CXX_FLAGS=" "-DVCPKG_CXX_FLAGS_RELEASE=" "-DVCPKG_CXX_FLAGS_DEBUG=" "-DVCPKG_C_FLAGS=" "-DVCPKG_C_FLAGS_RELEASE=" "-DVCPKG_C_FLAGS_DEBUG=" "-DVCPKG_CRT_LINKAGE=dynamic" "-DVCPKG_LINKER_FLAGS=" "-DVCPKG_LINKER_FLAGS_RELEASE=" "-DVCPKG_LINKER_FLAGS_DEBUG=" "-DVCPKG_TARGET_ARCHITECTURE=x64" "-DCMAKE_INSTALL_LIBDIR:STRING=lib" "-DCMAKE_INSTALL_BINDIR:STRING=bin" "-D_VCPKG_ROOT_DIR=D:/github/miyanyan/vcpkg" "-D_VCPKG_INSTALLED_DIR=D:/github/miyanyan/vcpkg/installed" "-DVCPKG_MANIFEST_INSTALL=OFF"  
-- The CXX compiler identification is MSVC 19.44.35209.0  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: C:/Program Files/Microsoft Visual Studio/2022/Community/VC/Tools/MSVC/14.44.35207/bin/Hostx64/x64/cl.exe - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Boost: using versioned layout: include, bin, lib, lib/cmake  
-- Boost: Release build, shared libraries, MPI OFF, Python OFF, testing OFF  
-- Boost: libraries included: asio  
CMake Error at D:/github/miyanyan/vcpkg/scripts/buildsystems/vcpkg.cmake:896 (_find_package):  
  Could not find a package configuration file provided by "boost_asio_core"  
  (requested version 1.89.0) with any of the following names:  
  
    boost_asio_coreConfig.cmake  
    boost_asio_core-config.cmake  
  
  Add the installation prefix of "boost_asio_core" to CMAKE_PREFIX_PATH or  
  set "boost_asio_core_DIR" to a directory containing one of the above files.  
  If "boost_asio_core" provides a separate development package or SDK, be  
  sure it has been installed.  
Call Stack (most recent call first):  
  D:/github/miyanyan/vcpkg/installed/x64-windows/share/boost/BoostConfig.cmake:67 (find_package)  
  D:/github/miyanyan/vcpkg/installed/x64-windows/share/boost/BoostConfig.cmake:128 (boostcfg_find_component)  
  D:/github/miyanyan/vcpkg/installed/x64-windows/share/boost/vcpkg-cmake-wrapper.cmake:3 (_find_package)  
  D:/github/miyanyan/vcpkg/scripts/buildsystems/vcpkg.cmake:850 (include)  
  D:/github/miyanyan/vcpkg/installed/x64-windows/share/boost/cmake-build/BoostRoot.cmake:234 (find_package)  
  D:/github/miyanyan/vcpkg/installed/x64-windows/share/boost/cmake-build/BoostRoot.cmake:291 (__boost_scan_dependencies)  
  CMakeLists.txt:10 (include)  
  
  
-- Configuring incomplete, errors occurred!  
ninja: build stopped: subcommand failed.  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2025-08-17 07:26:15 UTC  
> Url: https://github.com/boostorg/cmake/issues/85#issuecomment-3194195186  

Reported upstream in https://github.com/chriskohlhoff/asio/issues/1660.

---

## Comment 2

> Username: pdimov  
> Created at: 2025-10-20 15:09:05 UTC  
> Url: https://github.com/boostorg/cmake/issues/85#issuecomment-3422500802  

I just tested Asio installation, and then use, and it works correctly. So this might be a vcpkg-specific issue.
