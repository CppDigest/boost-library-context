# #778 - conan b2 command cannot find vcvarsall.bat when VS2022 build tools are installed to a different directory from VS2022 [Open]

> Username: devivoc  
> Created at: 2023-05-23 20:08:24 UTC  
> Updated at: 2026-02-19 09:50:25 UTC  
> Url: https://github.com/boostorg/boost/issues/778  

After running into [this issue](https://github.com/conan-io/conan-center-index/issues/9047) and updating my b2 dependency, I ran into this issue.  
This issue occurs because Chocolatey installs Visual Studio 2022 build tools to `C:\Program Files (x86)\Microsoft Visual Studio\2022\Build Tools` but it installs Visual Studio 2022 itself to `C:\Program Files\Microsoft Visual Studio\2022\Professional`. This problem is acknowledged [here](https://github.com/jberezanski/ChocolateyPackages/issues/126). I also think it is possible on normal installations via the Visual Studio Installer, depending on what the Install Location is set at.  
  
Configuration:  
conan: 1.59.0  
boost: 1.81.0  
b2: 4.9.6  
python: 3.7.7  
  
The smallest action I can take to recreate this issue is:  
 - choco install visualstudio2022professional  
 - choco install visualstudio2022buildtools  
 - choco install visualstudio2022-workload-nativedesktop  
  
make sure the conan compiler is Visual Studio and the version 17  
```  
Configuration for profile default:  
  
[settings]  
os=Windows  
os_build=Windows  
arch=x86_64  
arch_build=x86_64  
compiler=Visual Studio  
compiler.version=17  
build_type=Debug  
[options]  
[conf]  
[build_requires]  
[env]  
```  
  
make sure you at least have conancenter as a conan remote   
`conancenter: https://center.conan.io [Verify SSL: True]`  
  
and then do   
`conan install boost/1.81.0@ --build=missing`  
  
here is the error output:  
```  
boost/1.81.0: Applying build-requirement: b2/4.9.6  
Downloading conan_sources.tgz completed [0.75k]  
Decompressing conan_sources.tgz completed [0.00k]  
boost/1.81.0: Configuring sources in C:\.conan\c8ce608d1ce0d\1\src  
Downloading boost_1_81_0.tar.bz2 completed [116013.43k]                                  boost/1.81.0: /1.81.0:  
boost/1.81.0:  
boost/1.81.0: Apply patch (conan): Fails the build when mpi is not configured  
boost/1.81.0: Apply patch (conan): Fails the build when there is no iconv backend  
boost/1.81.0: Building your package in C:\.conan\65292aba\1  
boost/1.81.0: Generator txt created conanbuildinfo.txt  
boost/1.81.0: Calling generate()  
boost/1.81.0: Aggregating env generators  
boost/1.81.0: Calling build()  
boost/1.81.0: WARN: replace_in_file didn't find pattern '/* thread_local */' in 'C:\.conan\c8ce608d1ce0d\1\src\boost\sta  
cktrace\detail\libbacktrace_impls.hpp' file.  
boost/1.81.0: WARN: replace_in_file didn't find pattern '/* static __thread */' in 'C:\.conan\c8ce608d1ce0d\1\src\boost\  
stacktrace\detail\libbacktrace_impls.hpp' file.  
boost/1.81.0: WARN: replace_in_file didn't find pattern 'local generic-os = [ set.difference $(all-os) : aix darwin vxwo  
rks solaris osf hpux ] ;' in 'C:\.conan\c8ce608d1ce0d\1\src\tools\build\src\tools\gcc.jam' file.  
boost/1.81.0: WARN: replace_in_file didn't find pattern 'local no-threading = android beos haiku sgi darwin vxworks ;' i  
n 'C:\.conan\c8ce608d1ce0d\1\src\tools\build\src\tools\gcc.jam' file.  
boost/1.81.0: WARN: Patching user-config.jam  
boost/1.81.0: WARN: Using the new toolchains and generators without specifying a build profile (e.g: -pr:b=default) is d  
iscouraged and might cause failures and unexpected behavior  
boost/1.81.0: WARN:  
using zlib : 1.2.13 : <include>"C:/Users/cdevivo/.conan/data/zlib/1.2.13/_/_/package/164640aad040835ac89882393a96d892006  
94f04/include" <search>"C:/Users/cdevivo/.conan/data/zlib/1.2.13/_/_/package/164640aad040835ac89882393a96d89200694f04/li  
b" <name>zlib ;  
using bzip2 : 1.0.8 : <include>"C:/Users/cdevivo/.conan/data/bzip2/1.0.8/_/_/package/d74f69022acf854c1ce6f27efa07ccbb650  
4e4b1/include" <search>"C:/Users/cdevivo/.conan/data/bzip2/1.0.8/_/_/package/d74f69022acf854c1ce6f27efa07ccbb6504e4b1/li  
b" <name>bz2 ;  
using "msvc" : 14.3 :  :  
 ;  
boost/1.81.0: WARN: b2 -q numa=on target-os=windows architecture=x86 address-model=64 binary-format=pe abi=ms --layout=s  
ystem --user-config=C:\.conan\c8ce608d1ce0d\1\src\tools\build\user-config.jam -sNO_ZLIB=0 -sNO_BZIP2=0 -sNO_LZMA=1 -sNO_  
ZSTD=1 boost.locale.icu=off --disable-icu boost.locale.iconv=off --disable-iconv runtime-link=shared runtime-debugging=o  
n threading=multi visibility=hidden link=static variant=debug --with-atomic --with-chrono --with-container --with-contex  
t --with-contract --with-coroutine --with-date_time --with-exception --with-fiber --with-filesystem --with-graph --with-  
iostreams --with-json --with-locale --with-log --with-math --with-nowide --with-program_options --with-random --with-reg  
ex --with-serialization --with-stacktrace --with-system --with-test --with-thread --with-timer --with-type_erasure --wit  
h-url --with-wave toolset=msvc pch=on linkflags="" cxxflags="-fPIC" install --prefix=C:\.conan\6caa724c0\1 -j8 --abbrevi  
ate-paths -d0 --debug-configuration --build-dir="C:\.conan\65292aba\1\build-debug"  
The system cannot find the path specified.  
boost/1.81.0:  
boost/1.81.0: ERROR: Package 'c16d614bb966cd4b029ded7ef4fdc14b694dd24c' build failed  
boost/1.81.0: WARN: Build folder C:\.conan\65292aba\1\build-debug  
ERROR: boost/1.81.0: Error in build() method, line 887  
        self.run(full_command)  
        ConanException: Error 1 while executing b2 -q numa=on target-os=windows architecture=x86 address-model=64 binary  
-format=pe abi=ms --layout=system --user-config=C:\.conan\c8ce608d1ce0d\1\src\tools\build\user-config.jam -sNO_ZLIB=0 -s  
NO_BZIP2=0 -sNO_LZMA=1 -sNO_ZSTD=1 boost.locale.icu=off --disable-icu boost.locale.iconv=off --disable-iconv runtime-lin  
k=shared runtime-debugging=on threading=multi visibility=hidden link=static variant=debug --with-atomic --with-chrono --  
with-container --with-context --with-contract --with-coroutine --with-date_time --with-exception --with-fiber --with-fil  
esystem --with-graph --with-iostreams --with-json --with-locale --with-log --with-math --with-nowide --with-program_opti  
ons --with-random --with-regex --with-serialization --with-stacktrace --with-system --with-test --with-thread --with-tim  
er --with-type_erasure --with-url --with-wave toolset=msvc pch=on linkflags="" cxxflags="-fPIC" install --prefix=C:\.con  
an\6caa724c0\1 -j8 --abbreviate-paths -d0 --debug-configuration --build-dir="C:\.conan\65292aba\1\build-debug"  
```  
  
Took me a while to figure out what the path was that it was missing. It could not find vcvarsall.bat. Inspecting the conanfile I found that the VCVars class in ~\Python\Python37\site-packages\conan\tools\microsoft\visual.py just appends the path "VC/Auxilary/Build/vcvarsall.bat" to wherever Visual Studio is installed, assuming that path will be correct.   
  
For my use, I was able to just use a hack in my conan config to override the installation path for VS2022 (I understand that I could also specify an install path with chocolatey for the build tools, or I could have edited the boost conanfile) because I only used one compiler and one version. That hack was   
  
`conan config set tools.microsoft.msbuild:installation_path="C:\Program Files (x86)\Microsoft Visual Studio\2022\Build Tools`  
  
While the assumption is being made by Microsoft's python, it still an assumption that can cause VS2022 configurations with boost to fail. The error message could have at least been more helpful.

---

## Comment 1

> Username: sdolender  
> Created at: 2026-02-19 09:50:25 UTC  
> Url: https://github.com/boostorg/boost/issues/778#issuecomment-3926025697  

I've noticed pretty much the same issue with Boost 1.79.0/B2 4.8-git but with a different signature.   
Found this thread while investigating.  
  
**Error:** `don't know how to make <p/<<Current Working Dir>>C:\BuildTools\VC\Tools\MSVC\14.44.35207\bin\Hostx64\vcvarsall.bat`  
  
**My setup:**  
- Visual Studio 2022 Build Tools  
- Build tools installed to: C:\BuildTools  
- Actual vcvarsall.bat location: C:\BuildTools\VC\Auxiliary\Build\vcvarsall.bat  
- Boost is looking in (wrong): C:\BuildTools\VC\Tools\MSVC\14.44.35207\bin\Hostx64\vcvarsall.bat  
  
**Workaround that worked for me:**  
Created a file symlink from the incorrect path to the correct location:  
```cmd  
mklink "C:\BuildTools\VC\Tools\MSVC\14.44.35207\bin\Hostx64\vcvarsall.bat" "C:\BuildTools\VC\Auxiliary\Build\vcvarsall.bat"  
```
