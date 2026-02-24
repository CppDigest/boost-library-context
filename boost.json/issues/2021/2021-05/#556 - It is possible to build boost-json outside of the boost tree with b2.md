# #556 - It is possible to build boost-json outside of the boost tree with b2? [Closed]

> Username: autoantwort  
> Created at: 2021-05-02 20:37:42 UTC  
> Updated at: 2021-05-02 21:22:27 UTC  
> Closed at: 2021-05-02 21:19:34 UTC  
> Url: https://github.com/boostorg/json/issues/556  

In vcpkg every boost lib is its own lib, so we do not clone the boost meta repo recursively and build the whole thing. Every lib is cloned and build separately.    
So this lib will be build with the following command:    
`  
cd /mnt/vcpkg-ci/buildtrees/boost-json/src/ost-1.75.0-71b8ba6dd3.clean/build && /mnt/vcpkg-ci/installed/x64-linux/tools/boost-build/b2 --user-config=/mnt/vcpkg-ci/buildtrees/boost-json/x64-linux-rel/user-config.jam --stagedir=/mnt/vcpkg-ci/buildtrees/boost-json/x64-linux-rel/stage --build-dir=/mnt/vcpkg-ci/buildtrees/boost-json/x64-linux-rel runtime-link=static link=static address-model=64 architecture=x86 target-os=linux toolset=gcc threadapi=pthread -sZLIB_BINARY=z -sZLIB_LIBPATH="/mnt/vcpkg-ci/installed/x64-linux/lib/libz.a" -sBZIP2_BINARY=bz2 -sBZIP2_LIBPATH="/mnt/vcpkg-ci/installed/x64-linux/lib/libbz2.a" variant=release --layout=system --with-atomic --with-random --with-date_time --with-filesystem --with-system --with-thread --with-chrono -sZLIB_INCLUDE="/mnt/vcpkg-ci/installed/x64-linux/include" -sBZIP2_INCLUDE="/mnt/vcpkg-ci/installed/x64-linux/include" -sICU_PATH="/mnt/vcpkg-ci/installed/x64-linux" -j16 -sBOOST_ROOT=/mnt/vcpkg-ci/installed/x64-linux/tools/boost-build -sBOOST_BUILD_PATH=/mnt/vcpkg-ci/installed/x64-linux/tools/boost-build --debug-configuration --ignore-site-config --hash -q threading=multi debug-symbols=on stage  
`  
This works for the most libs, but not for this one because is looks outside of this repository:  
https://github.com/boostorg/json/blob/aae1863def3fb605d77ebbefc851cc26ac1758a9/Jamfile#L10  
  
For completeness the resulting error log:  
<details>  
<summary>Click to expand</summary>  
  
```  
notice: found boost-build.jam at /mnt/vcpkg-ci/installed/x64-linux/tools/boost-build/boost-build.jam  
notice: loading B2 from /mnt/vcpkg-ci/installed/x64-linux/tools/boost-build/src/kernel/bootstrap.jam  
notice: Site configuration files will be ignored due to the  
notice: --ignore-site-config command-line option.  
notice: Loading explicitly specified user configuration file:  
    /mnt/vcpkg-ci/buildtrees/boost-json/x64-linux-rel/user-config.jam  
notice: Searching '/mnt/vcpkg-ci/buildtrees/boost-json/x64-linux-rel' for user-config configuration file 'user-config.jam'.  
notice: Loading user-config configuration file 'user-config.jam' from '/mnt/vcpkg-ci/buildtrees/boost-json/x64-linux-rel'.  
notice: will use '/usr/bin/c++' for gcc, condition <toolset>gcc-5.4.1  
notice: using gcc libraries :: <toolset>gcc-5.4.1 :: /usr/bin /usr/lib /usr/lib32 /usr/lib64  
notice: using gcc archiver :: <toolset>gcc-5.4.1 :: /usr/bin/ar  
notice: using gcc ranlib :: <toolset>gcc-5.4.1 :: /usr/bin/ranlib  
warning: toolset gcc initialization: can not find tool windres  
warning: initialized from /mnt/vcpkg-ci/buildtrees/boost-json/x64-linux-rel/user-config.jam:22  
notice: using rc compiler :: <toolset>gcc-5.4.1 :: /usr/bin/as  
config.jam: No such file or directory  
/mnt/vcpkg-ci/installed/x64-linux/tools/boost-build/src/kernel/modules.jam:314: in modules.import  
IMPORT error: rule "requires" unknown in module "config".  
/mnt/vcpkg-ci/installed/x64-linux/tools/boost-build/src/build/project.jam:1121: in import  
../Jamfile:10: in load-aux  
/mnt/vcpkg-ci/installed/x64-linux/tools/boost-build/src/build/project.jam:378: in load-jamfile  
/mnt/vcpkg-ci/installed/x64-linux/tools/boost-build/src/build/project.jam:64: in load  
/mnt/vcpkg-ci/installed/x64-linux/tools/boost-build/src/build/project.jam:109: in load-parent  
/mnt/vcpkg-ci/installed/x64-linux/tools/boost-build/src/build/project.jam:513: in initialize  
/mnt/vcpkg-ci/installed/x64-linux/tools/boost-build/src/build/project.jam:348: in load-jamfile  
/mnt/vcpkg-ci/installed/x64-linux/tools/boost-build/src/build/project.jam:64: in load  
/mnt/vcpkg-ci/installed/x64-linux/tools/boost-build/src/build/project.jam:142: in project.find  
/mnt/vcpkg-ci/installed/x64-linux/tools/boost-build/src/build-system.jam:618: in load  
/mnt/vcpkg-ci/installed/x64-linux/tools/boost-build/src/kernel/modules.jam:295: in import  
/mnt/vcpkg-ci/installed/x64-linux/tools/boost-build/src/kernel/bootstrap.jam:139: in boost-build  
/mnt/vcpkg-ci/installed/x64-linux/tools/boost-build/boost-build.jam:8: in module scope  
ninja: build stopped: subcommand failed.  
```  
</details>   
  
Is there a way to build this lib with b2 outside of the boost tree?

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-05-02 20:41:31 UTC  
> Url: https://github.com/boostorg/json/issues/556#issuecomment-830868702  

I'm confused. Are you trying to build the library in standalone mode or are you trying to build it in Boost mode, but not in the Boost tree?

---

## Comment 2

> Username: autoantwort  
> Created at: 2021-05-02 20:42:20 UTC  
> Updated at: 2021-05-02 20:43:55 UTC  
> Url: https://github.com/boostorg/json/issues/556#issuecomment-830868805  

> are you trying to build it in Boost mode, but not in the Boost tree?  
  
This one. I have updated the Issue

---

## Comment 3

> Username: grisumbras  
> Created at: 2021-05-02 20:56:06 UTC  
> Url: https://github.com/boostorg/json/issues/556#issuecomment-830870589  

Well, it is technically possible but probably not very easy. Aside from requiring Boost.Config jam files you also need target `/boost//container` defined (I don't think we link to Boost.Container any more but the target is still used).

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-05-02 20:59:45 UTC  
> Url: https://github.com/boostorg/json/issues/556#issuecomment-830871021  

Building outside the boost tree with b2 is possible, but I think what you're really asking is "Does the Jamfile that comes with Boost.JSON support out-of-tree builds" to which the answer is an emphatic no. Bjam and CMake are not my specialties so I only can support what is necessary for my workflow and for the Boost libraries.

---

## Comment 5

> Username: grisumbras  
> Created at: 2021-05-02 21:03:23 UTC  
> Url: https://github.com/boostorg/json/issues/556#issuecomment-830871563  

BTW, I've grepped for other cases where Boost.Config jam module is used. Aside from tests and examples, I've got these:  
```  
context/build/Jamfile.v2|15| 12: import ../../config/checks/config : requires ;  
fiber/build/Jamfile.v2|12| 11:import ../../config/checks/config : requires ;  
beast/Jamfile|17| 8:import ../config/checks/config : requires ;  
contract/build/boost_contract_build.jam|52| 11:import ../../config/checks/config : requires ;  
multiprecision/config/Jamfile.v2|8| 11:import ../../config/checks/config : requires ;  
```  
At least a few of them aren't header only.

---

## Comment 6

> Username: autoantwort  
> Created at: 2021-05-02 21:10:01 UTC  
> Updated at: 2021-05-02 21:10:24 UTC  
> Url: https://github.com/boostorg/json/issues/556#issuecomment-830872483  

Yeah in the other ports something similar to the following is done:  
```cmake  
file(READ "${SOURCE_PATH}/build/Jamfile" _contents)  
string(REPLACE "import ../../config/checks/config" "import config/checks/config" _contents "${_contents}")  
file(WRITE "${SOURCE_PATH}/build/Jamfile" "${_contents}")  
  
file(READ "${SOURCE_PATH}/Jamfile" _contents)  
string(REPLACE "import ../config/checks/config" "import build/config/checks/config" _contents "${_contents}")  
file(WRITE "${SOURCE_PATH}/Jamfile" "${_contents}")  
  
file(COPY "${CURRENT_INSTALLED_DIR}/share/boost-config/checks" DESTINATION "${SOURCE_PATH}/build/config")  
```  
But with this patches I got the following errors:  
```  
notice: found boost-build.jam at /Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg_installed/arm64-osx/tools/boost-build/boost-build.jam  
notice: loading B2 from /Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg_installed/arm64-osx/tools/boost-build/src/kernel/bootstrap.jam  
notice: Site configuration files will be ignored due to the  
notice: --ignore-site-config command-line option.  
notice: Loading explicitly specified user configuration file:  
    /Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg/buildtrees/boost-json/x64-osx-rel/user-config.jam  
notice: Searching '/Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg/buildtrees/boost-json/x64-osx-rel' for user-config configuration file 'user-config.jam'.  
notice: Loading user-config configuration file 'user-config.jam' from '/Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg/buildtrees/boost-json/x64-osx-rel'.  
notice: will use '/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++' for gcc, condition <toolset>gcc-5.4.1  
notice: using gcc libraries :: <toolset>gcc-5.4.1 :: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib32 /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib64  
notice: using gcc archiver :: <toolset>gcc-5.4.1 :: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ar  
notice: using gcc ranlib :: <toolset>gcc-5.4.1 :: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ranlib  
warning: toolset gcc initialization: can not find tool windres  
warning: initialized from /Users/leanderSchulten/git_projekte/Lichtsteuerung/vcpkg/buildtrees/boost-json/x64-osx-rel/user-config.jam:22  
notice: using rc compiler :: <toolset>gcc-5.4.1 :: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/as  
notice: [cmdline-cfg] toolset clang not previously configured; attempting to auto-configure now  
notice: will use 'clang++' for clang-darwin, condition <toolset>clang-12.0  
Performing configuration checks  
  
    - cxx11_variadic_templates : yes  
    - cxx11_template_aliases   : yes  
    - cxx11_hdr_tuple          : yes  
    - cxx11_decltype           : yes  
    - cxx11_constexpr          : yes  
    - cxx11_alignas            : yes  
error: Unable to find file or target named  
error:     '/boost//container'  
error: referred to from project at  
error:     '../build'  
  
ninja: build stopped: subcommand failed.  
```  
PS: I also tried it in the past, but didn't know what to with the error. But you also said that this will happen

---

## Comment 7

> Username: autoantwort  
> Created at: 2021-05-02 21:19:06 UTC  
> Url: https://github.com/boostorg/json/issues/556#issuecomment-830873711  

Ok, I simply removed this line:  
https://github.com/boostorg/json/blob/aae1863def3fb605d77ebbefc851cc26ac1758a9/build/Jamfile#L17  
Works now :D    
Thank you!

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-05-02 21:22:27 UTC  
> Url: https://github.com/boostorg/json/issues/556#issuecomment-830874178  

LOL.... that's serendipitous :)
