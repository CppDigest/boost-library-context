# #724 - "error: Recursion in main target references" when upgrading to 1.76.0 [Open]

> Username: autoantwort  
> Created at: 2021-05-06 15:03:15 UTC  
> Updated at: 2021-05-29 16:22:45 UTC  
> Url: https://github.com/boostorg/build/issues/724  

In vcpkg every boost lib is its own lib, so we do not clone the boost meta repo recursively and build the whole thing. Every lib is cloned and build separately (outside of the boost tree).   
  
One problem is that some libs like nowide imports files from outside the repository like [here](https://github.com/boostorg/nowide/blob/8c18f90c7a9a4791424218b570e060d4a460983f/build/Jamfile.v2#L11)  
Until now it was possible to workaround that by changing the import location and copying the necessary files:  
```cmake  
file(READ "${SOURCE_PATH}/build/Jamfile.v2" _contents)  
string(REPLACE "import ../../config/checks/config" "import config/checks/config" _contents "${_contents}")  
string(REPLACE "check-target-builds ../config//cxx11_moveable_fstreams" "check-target-builds ../check_movable_fstreams.cpp" _contents "${_contents}")  
string(REPLACE "check-target-builds ../config//lfs_support" "check-target-builds ../check_lfs_support.cpp" _contents "${_contents}")  
file(WRITE "${SOURCE_PATH}/build/Jamfile.v2" "${_contents}")  
file(COPY "${CURRENT_INSTALLED_DIR}/share/boost-config/checks" DESTINATION "${SOURCE_PATH}/build/config")  
file(COPY "${SOURCE_PATH}/config/check_lfs_support.cpp" "${SOURCE_PATH}/config/check_movable_fstreams.cpp" DESTINATION "${SOURCE_PATH}/build/config")  
```  
  
But now with boost build 1.76.0 we get the following error:  
```  
[1/2] cd /Users/xx/vcpkg/buildtrees/boost-nowide/src/ost-1.75.0-21f8bbc965.clean/build && /Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/b2 --user-config=/Users/xx/vcpkg/buildtrees/boost-nowide/x64-osx-rel/user-config.jam --stagedir=/Users/xx/vcpkg/buildtrees/boost-nowide/x64-osx-rel/stage --build-dir=/Users/xx/vcpkg/buildtrees/boost-nowide/x64-osx-rel cxxstd=11 runtime-link=static link=static address-model=64 architecture=x86 target-os=darwin toolset=clang threadapi=pthread -sZLIB_BINARY=z -sZLIB_LIBPATH="/Users/xx/vcpkg_installed/x64-osx/lib/libz.a" -sBZIP2_BINARY=bz2 -sBZIP2_LIBPATH="/Users/xx/vcpkg_installed/x64-osx/lib/libbz2.a" variant=release --layout=system --with-atomic --with-random --with-date_time --with-filesystem --with-system --with-thread --with-chrono -sZLIB_INCLUDE="/Users/xx/vcpkg_installed/x64-osx/include" -sBZIP2_INCLUDE="/Users/xx/vcpkg_installed/x64-osx/include" -sICU_PATH="ICU_PATH-NOTFOUND" -j8 -sBOOST_ROOT=/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build -sBOOST_BUILD_PATH=/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build --debug-configuration --ignore-site-config --hash -q threading=multi debug-symbols=on stage  
FAILED: CMakeFiles/boost   
cd /Users/xx/vcpkg/buildtrees/boost-nowide/src/ost-1.75.0-21f8bbc965.clean/build && /Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/b2 --user-config=/Users/xx/vcpkg/buildtrees/boost-nowide/x64-osx-rel/user-config.jam --stagedir=/Users/xx/vcpkg/buildtrees/boost-nowide/x64-osx-rel/stage --build-dir=/Users/xx/vcpkg/buildtrees/boost-nowide/x64-osx-rel cxxstd=11 runtime-link=static link=static address-model=64 architecture=x86 target-os=darwin toolset=clang threadapi=pthread -sZLIB_BINARY=z -sZLIB_LIBPATH="/Users/xx/vcpkg_installed/x64-osx/lib/libz.a" -sBZIP2_BINARY=bz2 -sBZIP2_LIBPATH="/Users/xx/vcpkg_installed/x64-osx/lib/libbz2.a" variant=release --layout=system --with-atomic --with-random --with-date_time --with-filesystem --with-system --with-thread --with-chrono -sZLIB_INCLUDE="/Users/xx/vcpkg_installed/x64-osx/include" -sBZIP2_INCLUDE="/Users/xx/vcpkg_installed/x64-osx/include" -sICU_PATH="ICU_PATH-NOTFOUND" -j8 -sBOOST_ROOT=/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build -sBOOST_BUILD_PATH=/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build --debug-configuration --ignore-site-config --hash -q threading=multi debug-symbols=on stage  
notice: found boost-build.jam at /Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/boost-build.jam  
notice: loading B2 from /Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/kernel/bootstrap.jam  
notice: Site configuration files will be ignored due to the  
notice: --ignore-site-config command-line option.  
notice: Loading explicitly specified user configuration file:  
    /Users/xx/vcpkg/buildtrees/boost-nowide/x64-osx-rel/user-config.jam  
notice: Searching '/Users/xx/vcpkg/buildtrees/boost-nowide/x64-osx-rel' for user-config configuration file 'user-config.jam'.  
notice: Loading user-config configuration file 'user-config.jam' from '/Users/xx/vcpkg/buildtrees/boost-nowide/x64-osx-rel'.  
notice: will use '/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++' for gcc, condition <toolset>gcc-5.4.1  
notice: using gcc libraries :: <toolset>gcc-5.4.1 :: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib32 /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib64  
notice: using gcc archiver :: <toolset>gcc-5.4.1 :: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ar  
notice: using gcc ranlib :: <toolset>gcc-5.4.1 :: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ranlib  
warning: toolset gcc initialization: can not find tool windres  
warning: initialized from /Users/xx/vcpkg/buildtrees/boost-nowide/x64-osx-rel/user-config.jam:22  
notice: using rc compiler :: <toolset>gcc-5.4.1 :: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/as  
notice: [cmdline-cfg] toolset clang not previously configured; attempting to auto-configure now  
notice: will use 'clang++' for clang-darwin, condition <toolset>clang-12.0  
Performing configuration checks  
  
    - std::fstream is moveable and swappable : yes [1]  
    - Has Large File Support   : yes [1]  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:609: in start-building from module targets  
error: Recursion in main target references  
error: the following target are being built currently:  
error: ../build/stage /Users/xx/vcpkg/buildtrees/boost-nowide/src/ost-1.75.0-21f8bbc965.clean/build/config/checks/cxx11_defaulted_functions /Users/xx/vcpkg/buildtrees/boost-nowide/src/ost-1.75.0-21f8bbc965.clean/build/config/checks/cxx11_noexcept /Users/xx/vcpkg/buildtrees/boost-nowide/src/ost-1.75.0-21f8bbc965.clean/build/config/checks/cxx11_rvalue_references /Users/xx/vcpkg/buildtrees/boost-nowide/src/ost-1.75.0-21f8bbc965.clean/build/config/checks/cxx11_static_assert /Users/xx/vcpkg/buildtrees/boost-nowide/src/ost-1.75.0-21f8bbc965.clean/build/config/checks/cxx11_defaulted_functions  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:776: in class@main-target.generate from module object(main-target)@1431  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:934: in targets.generate-from-reference from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/configure.jam:355: in builds-raw from module configure  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/configure.jam:457: in configure.builds from module configure  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/configure.jam:512: in check from module object(check-target-builds-worker)@211  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/kernel/modules.jam:107: in modules.call-in from module object(check-target-builds-worker)@211  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/util/indirect.jam:105: in indirect.call from module indirect  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/property.jam:132: in property.evaluate-conditionals-in-context from module property  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1088: in evaluate-requirements from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1122: in common-properties2 from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1018: in targets.common-properties from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1314: in class@basic-target.generate from module object(typed-target)@913  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:812: in generate-really from module object(main-target)@1479  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:784: in class@main-target.generate from module object(main-target)@1479  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:934: in targets.generate-from-reference from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/configure.jam:355: in builds-raw from module configure  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/configure.jam:457: in configure.builds from module configure  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/configure.jam:512: in check from module object(check-target-builds-worker)@214  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/kernel/modules.jam:107: in modules.call-in from module object(check-target-builds-worker)@214  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/util/indirect.jam:105: in indirect.call from module indirect  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/property.jam:132: in property.evaluate-conditionals-in-context from module property  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1088: in evaluate-requirements from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1122: in common-properties2 from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1018: in targets.common-properties from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1314: in class@basic-target.generate from module object(typed-target)@893  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:812: in generate-really from module object(main-target)@1474  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:784: in class@main-target.generate from module object(main-target)@1474  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:934: in targets.generate-from-reference from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/configure.jam:355: in builds-raw from module configure  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/configure.jam:457: in configure.builds from module configure  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/configure.jam:512: in check from module object(check-target-builds-worker)@213  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/kernel/modules.jam:107: in modules.call-in from module object(check-target-builds-worker)@213  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/util/indirect.jam:105: in indirect.call from module indirect  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/property.jam:132: in property.evaluate-conditionals-in-context from module property  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1088: in evaluate-requirements from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1122: in common-properties2 from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1018: in targets.common-properties from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1314: in class@basic-target.generate from module object(typed-target)@845  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:812: in generate-really from module object(main-target)@1462  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:784: in class@main-target.generate from module object(main-target)@1462  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:934: in targets.generate-from-reference from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/configure.jam:355: in builds-raw from module configure  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/configure.jam:457: in configure.builds from module configure  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/configure.jam:512: in check from module object(check-target-builds-worker)@212  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/kernel/modules.jam:107: in modules.call-in from module object(check-target-builds-worker)@212  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/util/indirect.jam:105: in indirect.call from module indirect  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/property.jam:132: in property.evaluate-conditionals-in-context from module property  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1088: in evaluate-requirements from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1122: in common-properties2 from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1018: in targets.common-properties from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1314: in class@basic-target.generate from module object(typed-target)@721  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:812: in generate-really from module object(main-target)@1431  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:784: in class@main-target.generate from module object(main-target)@1431  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:934: in targets.generate-from-reference from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/configure.jam:355: in builds-raw from module configure  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/configure.jam:457: in configure.builds from module configure  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/configure.jam:512: in check from module object(check-target-builds-worker)@211  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/kernel/modules.jam:107: in modules.call-in from module object(check-target-builds-worker)@211  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/util/indirect.jam:105: in indirect.call from module indirect  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/property.jam:132: in property.evaluate-conditionals-in-context from module property  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1088: in evaluate-requirements from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1122: in common-properties2 from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1018: in targets.common-properties from module targets  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:1314: in class@basic-target.generate from module object(install-target-class)@227  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:812: in generate-really from module object(main-target)@232  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build/targets.jam:784: in class@main-target.generate from module object(main-target)@232  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/build-system.jam:797: in load from module build-system  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/kernel/modules.jam:295: in import from module modules  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/src/kernel/bootstrap.jam:139: in boost-build from module  
/Users/xx/vcpkg_installed/arm64-osx/tools/boost-build/boost-build.jam:8: in module scope from module  
  
ninja: build stopped: subcommand failed.  
```  
  
Do you have an idea how to fix that or what has been chaneg that causes the recursion now?

---

## Comment 1

> Username: autoantwort  
> Created at: 2021-05-06 15:31:00 UTC  
> Updated at: 2021-05-06 16:00:39 UTC  
> Url: https://github.com/boostorg/build/issues/724#issuecomment-833617627  

Commit 95c875b1dd12a52f0d120480c8b498c9c3da36ab breaks it. Before this commit the build is successful, after this commit it is no longer successful.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:15 UTC  
> Url: https://github.com/boostorg/build/issues/724#issuecomment-850859562  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
