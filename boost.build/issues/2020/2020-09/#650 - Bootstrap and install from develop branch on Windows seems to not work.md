# #650 - Bootstrap and install from develop branch on Windows seems to not work. [Closed]

> Username: tee3  
> Created at: 2020-09-02 16:48:36 UTC  
> Updated at: 2020-09-27 12:56:42 UTC  
> Closed at: 2020-09-26 23:03:08 UTC  
> Url: https://github.com/boostorg/build/issues/650  

I recently tried to bootstrap and install from the latest `develop` branch on Windows and running the default install target failed to work.  I have Visual Studio Community 2019 installed on Windows 10.  
  
I'm assuming the following should result in a working `b2` given an empty `BOOST_BUILD_PATH`  
  
```bat  
.\bootstrap  
.\b2 install  
set PATH=C:\b2\bin;%PATH%  
set BOOST_BUILD_PATH=  
b2  
```  
  
I saw there were a lot of changes relative to how this all works, so maybe I'm misunderstanding what should happen here.  What should happen?  
  
The following is an abridged log of the bootstrap and install process.  
  
```console  
C:\TEMP> git clone https://github.com/boostorg/build.git  
rem ...  
C:\TEMP> cd build  
C:\TEMP\build> .\bootstrap  
rem ...  
C:\TEMP\build> .\b2 install  
..\b2 install  
Jamroot.jam:107: in modules.load from module Jamfile<C:\TEMP\build>  
warning: Bison generator program 'bison' not found. Skipping grammar build.  
c:/TEMP/build/src/build\project.jam:372: in load-jamfile from module project  
c:/TEMP/build/src/build\project.jam:64: in load from module project  
c:/TEMP/build/src/build\project.jam:142: in project.find from module project  
c:/TEMP/build/src\build-system.jam:618: in load from module build-system  
c:/TEMP/build/src/kernel\modules.jam:295: in import from module modules  
c:/TEMP/build/src/kernel/bootstrap.jam:139: in boost-build from module  
c:/TEMP/build/boost-build.jam:8: in module scope from module  
...found 476 targets...  
...updating 245 targets...  
common.copy C:\b2\share\boost-build\src\build-system.jam  
C:\TEMP\build\src\build-system.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\quickbook.jam  
C:\TEMP\build\src\tools\quickbook.jam  
        1 file(s) copied.  
common.copy C:\b2\bin\b2.exe  
C:\TEMP\build\src\engine\b2.exe  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\build\ac.jam  
C:\TEMP\build\src\build\ac.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\sass.jam  
C:\TEMP\build\src\tools\sass.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\saxonhe.jam  
C:\TEMP\build\src\tools\saxonhe.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\rc.jam  
C:\TEMP\build\src\tools\rc.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\stage.jam  
C:\TEMP\build\src\tools\stage.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\stlport.jam  
C:\TEMP\build\src\tools\stlport.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\sun.jam  
C:\TEMP\build\src\tools\sun.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\symlink.jam  
C:\TEMP\build\src\tools\symlink.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\testing-aux.jam  
C:\TEMP\build\src\tools\testing-aux.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\unix.jam  
C:\TEMP\build\src\tools\unix.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\testing.jam  
C:\TEMP\build\src\tools\testing.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\vacpp.jam  
C:\TEMP\build\src\tools\vacpp.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\vmsdecc.jam  
C:\TEMP\build\src\tools\vmsdecc.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\xlcpp.jam  
C:\TEMP\build\src\tools\xlcpp.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\whale.jam  
C:\TEMP\build\src\tools\whale.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\xlf.jam  
C:\TEMP\build\src\tools\xlf.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\xsltproc-config.jam  
C:\TEMP\build\src\tools\xsltproc-config.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\xsltproc.jam  
C:\TEMP\build\src\tools\xsltproc.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\zlib.jam  
C:\TEMP\build\src\tools\zlib.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\zstd.jam  
C:\TEMP\build\src\tools\zstd.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\doxygen\windows-paths-check.doxyfile  
C:\TEMP\build\src\tools\doxygen\windows-paths-check.doxyfile  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\address-model-feature.jam  
C:\TEMP\build\src\tools\features\address-model-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\adoc.jam  
C:\TEMP\build\src\tools\types\adoc.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\cpp.jam  
C:\TEMP\build\src\tools\types\cpp.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\generators\archive-generator.jam  
C:\TEMP\build\src\tools\generators\archive-generator.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\asm.jam  
C:\TEMP\build\src\tools\types\asm.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\css.jam  
C:\TEMP\build\src\tools\types\css.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\docbook.jam  
C:\TEMP\build\src\tools\types\docbook.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\exe.jam  
C:\TEMP\build\src\tools\types\exe.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\html.jam  
C:\TEMP\build\src\tools\types\html.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\lib.jam  
C:\TEMP\build\src\tools\types\lib.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\man.jam  
C:\TEMP\build\src\tools\types\man.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\markdown.jam  
C:\TEMP\build\src\tools\types\markdown.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\obj.jam  
C:\TEMP\build\src\tools\types\obj.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\pdf.jam  
C:\TEMP\build\src\tools\types\pdf.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\objc.jam  
C:\TEMP\build\src\tools\types\objc.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\preprocessed.jam  
C:\TEMP\build\src\tools\types\preprocessed.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\qt.jam  
C:\TEMP\build\src\tools\types\qt.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\register.jam  
C:\TEMP\build\src\tools\types\register.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\rsp.jam  
C:\TEMP\build\src\tools\types\rsp.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\sass-type.jam  
C:\TEMP\build\src\tools\types\sass-type.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\types\xml.jam  
C:\TEMP\build\src\tools\types\xml.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\generators\c-compiling-generator.jam  
C:\TEMP\build\src\tools\generators\c-compiling-generator.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\generators\dummy-generator.jam  
C:\TEMP\build\src\tools\generators\dummy-generator.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\generators\lib-generator.jam  
C:\TEMP\build\src\tools\generators\lib-generator.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\generators\linking-generator.jam  
C:\TEMP\build\src\tools\generators\linking-generator.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\generators\prebuilt-lib-generator.jam  
C:\TEMP\build\src\tools\generators\prebuilt-lib-generator.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\generators\searched-lib-generator.jam  
C:\TEMP\build\src\tools\generators\searched-lib-generator.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\generators\__init_generators__.jam  
C:\TEMP\build\src\tools\generators\__init_generators__.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\allow-feature.jam  
C:\TEMP\build\src\tools\features\allow-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\architecture-feature.jam  
C:\TEMP\build\src\tools\features\architecture-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\archiveflags-feature.jam  
C:\TEMP\build\src\tools\features\archiveflags-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\asmflags-feature.jam  
C:\TEMP\build\src\tools\features\asmflags-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\build-feature.jam  
C:\TEMP\build\src\tools\features\build-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\cflags-feature.jam  
C:\TEMP\build\src\tools\features\cflags-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\coverage-feature.jam  
C:\TEMP\build\src\tools\features\coverage-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\conditional-feature.jam  
C:\TEMP\build\src\tools\features\conditional-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\cxxabi-feature.jam  
C:\TEMP\build\src\tools\features\cxxabi-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\cxx-template-depth-feature.jam  
C:\TEMP\build\src\tools\features\cxx-template-depth-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\cxxflags-feature.jam  
C:\TEMP\build\src\tools\features\cxxflags-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\debug-feature.jam  
C:\TEMP\build\src\tools\features\debug-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\cxxstd-feature.jam  
C:\TEMP\build\src\tools\features\cxxstd-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\define-feature.jam  
C:\TEMP\build\src\tools\features\define-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\dependency-feature.jam  
C:\TEMP\build\src\tools\features\dependency-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\dll-feature.jam  
C:\TEMP\build\src\tools\features\dll-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\exception-feature.jam  
C:\TEMP\build\src\tools\features\exception-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\fflags-feature.jam  
C:\TEMP\build\src\tools\features\fflags-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\find-lib-feature.jam  
C:\TEMP\build\src\tools\features\find-lib-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\file-feature.jam  
C:\TEMP\build\src\tools\features\file-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\flags-feature.jam  
C:\TEMP\build\src\tools\features\flags-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\force-include-feature.jam  
C:\TEMP\build\src\tools\features\force-include-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\include-feature.jam  
C:\TEMP\build\src\tools\features\include-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\instruction-set-feature.jam  
C:\TEMP\build\src\tools\features\instruction-set-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\internal-feature.jam  
C:\TEMP\build\src\tools\features\internal-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\linkflags-feature.jam  
C:\TEMP\build\src\tools\features\linkflags-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\link-feature.jam  
C:\TEMP\build\src\tools\features\link-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\library-feature.jam  
C:\TEMP\build\src\tools\features\library-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\local-visibility-feature.jam  
C:\TEMP\build\src\tools\features\local-visibility-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\location-prefix-feature.jam  
C:\TEMP\build\src\tools\features\location-prefix-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\location-feature.jam  
C:\TEMP\build\src\tools\features\location-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\lto-feature.jam  
C:\TEMP\build\src\tools\features\lto-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\name-feature.jam  
C:\TEMP\build\src\tools\features\name-feature.jam  
        1 file(s) copied.  
...on 100th target...  
common.copy C:\b2\share\boost-build\src\tools\features\objcflags-feature.jam  
C:\TEMP\build\src\tools\features\objcflags-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\optimization-feature.jam  
C:\TEMP\build\src\tools\features\optimization-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\os-feature.jam  
C:\TEMP\build\src\tools\features\os-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\relevant-feature.jam  
C:\TEMP\build\src\tools\features\relevant-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\rtti-feature.jam  
C:\TEMP\build\src\tools\features\rtti-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\runtime-feature.jam  
C:\TEMP\build\src\tools\features\runtime-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\sanitizers-feature.jam  
C:\TEMP\build\src\tools\features\sanitizers-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\search-feature.jam  
C:\TEMP\build\src\tools\features\search-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\source-feature.jam  
C:\TEMP\build\src\tools\features\source-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\stdlib-feature.jam  
C:\TEMP\build\src\tools\features\stdlib-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\strip-feature.jam  
C:\TEMP\build\src\tools\features\strip-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\threadapi-feature.jam  
C:\TEMP\build\src\tools\features\threadapi-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\tag-feature.jam  
C:\TEMP\build\src\tools\features\tag-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\threading-feature.jam  
C:\TEMP\build\src\tools\features\threading-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\toolset-feature.jam  
C:\TEMP\build\src\tools\features\toolset-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\translate-path-feature.jam  
C:\TEMP\build\src\tools\features\translate-path-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\user-interface-feature.jam  
C:\TEMP\build\src\tools\features\user-interface-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\variant-feature.jam  
C:\TEMP\build\src\tools\features\variant-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\version-feature.jam  
C:\TEMP\build\src\tools\features\version-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\visibility-feature.jam  
C:\TEMP\build\src\tools\features\visibility-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\warnings-feature.jam  
C:\TEMP\build\src\tools\features\warnings-feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\features\__init_features__.jam  
C:\TEMP\build\src\tools\features\__init_features__.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\build\alias.jam  
C:\TEMP\build\src\build\alias.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\doxygen\windows-paths-check.hpp  
C:\TEMP\build\src\tools\doxygen\windows-paths-check.hpp  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\build\build-request.jam  
C:\TEMP\build\src\build\build-request.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\xsltproc\included.xsl  
C:\TEMP\build\src\tools\xsltproc\included.xsl  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\xsltproc\test.xml  
C:\TEMP\build\src\tools\xsltproc\test.xml  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\build\config-cache.jam  
C:\TEMP\build\src\build\config-cache.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\xsltproc\test.xsl  
C:\TEMP\build\src\tools\xsltproc\test.xsl  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\build\configure.jam  
C:\TEMP\build\src\build\configure.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\build\feature.jam  
C:\TEMP\build\src\build\feature.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\build\generators.jam  
C:\TEMP\build\src\build\generators.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\build\project.jam  
C:\TEMP\build\src\build\project.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\build\property-set.jam  
C:\TEMP\build\src\build\property-set.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\build\property.jam  
C:\TEMP\build\src\build\property.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\build\scanner.jam  
C:\TEMP\build\src\build\scanner.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\build\targets.jam  
C:\TEMP\build\src\build\targets.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\build\toolset.jam  
C:\TEMP\build\src\build\toolset.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\build\type.jam  
C:\TEMP\build\src\build\type.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\build\version.jam  
C:\TEMP\build\src\build\version.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\build\virtual-target.jam  
C:\TEMP\build\src\build\virtual-target.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\contrib\modular.jam  
C:\TEMP\build\src\contrib\modular.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\contrib\boost.jam  
C:\TEMP\build\src\contrib\boost.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\kernel\boost-build.jam  
C:\TEMP\build\src\kernel\boost-build.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\contrib\tntnet.jam  
C:\TEMP\build\src\contrib\tntnet.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\contrib\wxFormBuilder.jam  
C:\TEMP\build\src\contrib\wxFormBuilder.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\kernel\bootstrap.jam  
C:\TEMP\build\src\kernel\bootstrap.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\kernel\class.jam  
C:\TEMP\build\src\kernel\class.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\kernel\errors.jam  
C:\TEMP\build\src\kernel\errors.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\kernel\modules.jam  
C:\TEMP\build\src\kernel\modules.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\acc.jam  
C:\TEMP\build\src\tools\acc.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\options\help.jam  
C:\TEMP\build\src\options\help.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\asciidoctor.jam  
C:\TEMP\build\src\tools\asciidoctor.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\util\assert.jam  
C:\TEMP\build\src\util\assert.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\util\container.jam  
C:\TEMP\build\src\util\container.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\util\doc.jam  
C:\TEMP\build\src\util\doc.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\util\indirect.jam  
C:\TEMP\build\src\util\indirect.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\util\numbers.jam  
C:\TEMP\build\src\util\numbers.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\util\order.jam  
C:\TEMP\build\src\util\order.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\util\os.jam  
C:\TEMP\build\src\util\os.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\util\option.jam  
C:\TEMP\build\src\util\option.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\util\param.jam  
C:\TEMP\build\src\util\param.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\util\path.jam  
C:\TEMP\build\src\util\path.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\util\print.jam  
C:\TEMP\build\src\util\print.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\util\sequence.jam  
C:\TEMP\build\src\util\sequence.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\util\regex.jam  
C:\TEMP\build\src\util\regex.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\util\set.jam  
C:\TEMP\build\src\util\set.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\util\string.jam  
C:\TEMP\build\src\util\string.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\util\utility.jam  
C:\TEMP\build\src\util\utility.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\bison.jam  
C:\TEMP\build\src\tools\bison.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\auto-index.jam  
C:\TEMP\build\src\tools\auto-index.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\boostbook-config.jam  
C:\TEMP\build\src\tools\boostbook-config.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\boostbook.jam  
C:\TEMP\build\src\tools\boostbook.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\builtin.jam  
C:\TEMP\build\src\tools\builtin.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\borland.jam  
C:\TEMP\build\src\tools\borland.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\bzip2.jam  
C:\TEMP\build\src\tools\bzip2.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\cast.jam  
C:\TEMP\build\src\tools\cast.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\clang-darwin.jam  
C:\TEMP\build\src\tools\clang-darwin.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\clang-linux.jam  
C:\TEMP\build\src\tools\clang-linux.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\clang-vxworks.jam  
C:\TEMP\build\src\tools\clang-vxworks.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\clang-win.jam  
C:\TEMP\build\src\tools\clang-win.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\clang.jam  
C:\TEMP\build\src\tools\clang.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\common.jam  
C:\TEMP\build\src\tools\common.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\como-linux.jam  
C:\TEMP\build\src\tools\como-linux.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\como-win.jam  
C:\TEMP\build\src\tools\como-win.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\como.jam  
C:\TEMP\build\src\tools\como.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\convert.jam  
C:\TEMP\build\src\tools\convert.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\cw-config.jam  
C:\TEMP\build\src\tools\cw-config.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\cray.jam  
C:\TEMP\build\src\tools\cray.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\cw.jam  
C:\TEMP\build\src\tools\cw.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\cygwin.jam  
C:\TEMP\build\src\tools\cygwin.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\diab.jam  
C:\TEMP\build\src\tools\diab.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\darwin.jam  
C:\TEMP\build\src\tools\darwin.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\docutils.jam  
C:\TEMP\build\src\tools\docutils.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\dmc.jam  
C:\TEMP\build\src\tools\dmc.jam  
        1 file(s) copied.  
...on 200th target...  
common.copy C:\b2\share\boost-build\src\tools\doxygen-config.jam  
C:\TEMP\build\src\tools\doxygen-config.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\doxproc.py  
C:\TEMP\build\src\tools\doxproc.py  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\doxygen.jam  
C:\TEMP\build\src\tools\doxygen.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\emscripten.jam  
C:\TEMP\build\src\tools\emscripten.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\embarcadero.jam  
C:\TEMP\build\src\tools\embarcadero.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\flags.jam  
C:\TEMP\build\src\tools\flags.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\fop.jam  
C:\TEMP\build\src\tools\fop.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\fortran.jam  
C:\TEMP\build\src\tools\fortran.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\gcc.jam  
C:\TEMP\build\src\tools\gcc.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\generate.jam  
C:\TEMP\build\src\tools\generate.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\gettext.jam  
C:\TEMP\build\src\tools\gettext.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\gfortran.jam  
C:\TEMP\build\src\tools\gfortran.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\hpfortran.jam  
C:\TEMP\build\src\tools\hpfortran.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\hp_cxx.jam  
C:\TEMP\build\src\tools\hp_cxx.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\ifort.jam  
C:\TEMP\build\src\tools\ifort.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\intel-linux.jam  
C:\TEMP\build\src\tools\intel-linux.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\intel-vxworks.jam  
C:\TEMP\build\src\tools\intel-vxworks.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\intel-darwin.jam  
C:\TEMP\build\src\tools\intel-darwin.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\intel-win.jam  
C:\TEMP\build\src\tools\intel-win.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\lex.jam  
C:\TEMP\build\src\tools\lex.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\libpng.jam  
C:\TEMP\build\src\tools\libpng.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\libjpeg.jam  
C:\TEMP\build\src\tools\libjpeg.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\intel.jam  
C:\TEMP\build\src\tools\intel.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\libtiff.jam  
C:\TEMP\build\src\tools\libtiff.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\link.jam  
C:\TEMP\build\src\tools\link.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\lzma.jam  
C:\TEMP\build\src\tools\lzma.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\make.jam  
C:\TEMP\build\src\tools\make.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\mc.jam  
C:\TEMP\build\src\tools\mc.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\message.jam  
C:\TEMP\build\src\tools\message.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\midl.jam  
C:\TEMP\build\src\tools\midl.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\mipspro.jam  
C:\TEMP\build\src\tools\mipspro.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\mpi.jam  
C:\TEMP\build\src\tools\mpi.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\msvc-config.jam  
C:\TEMP\build\src\tools\msvc-config.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\notfile.jam  
C:\TEMP\build\src\tools\notfile.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\msvc.jam  
C:\TEMP\build\src\tools\msvc.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\openssl.jam  
C:\TEMP\build\src\tools\openssl.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\package.jam  
C:\TEMP\build\src\tools\package.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\pathscale.jam  
C:\TEMP\build\src\tools\pathscale.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\pch.jam  
C:\TEMP\build\src\tools\pch.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\pgi.jam  
C:\TEMP\build\src\tools\pgi.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\python-config.jam  
C:\TEMP\build\src\tools\python-config.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\pkg-config.jam  
C:\TEMP\build\src\tools\pkg-config.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\python.jam  
C:\TEMP\build\src\tools\python.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\qt3.jam  
C:\TEMP\build\src\tools\qt3.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\qcc.jam  
C:\TEMP\build\src\tools\qcc.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\qt.jam  
C:\TEMP\build\src\tools\qt.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\qt4.jam  
C:\TEMP\build\src\tools\qt4.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\qt5.jam  
C:\TEMP\build\src\tools\qt5.jam  
        1 file(s) copied.  
common.copy C:\b2\share\boost-build\src\tools\quickbook-config.jam  
C:\TEMP\build\src\tools\quickbook-config.jam  
        1 file(s) copied.  
...updated 245 targets...  
C:\TEMP\build> cd \tmp  
C:\TEMP> set PATH=C:\b2\bin;%PATH%  
C:\TEMP> set BOOST_BUILD_PATH=  
C:\TEMP> b2 -v  
B2 Version 4.4. OS=NT.  
  Copyright 1993-2002 Christopher Seiwald and Perforce Software, Inc.  
  Copyright 2001 David Turner.  
  Copyright 2001-2004 David Abrahams.  
  Copyright 2002-2019 Rene Rivera.  
  Copyright 2003-2015 Vladimir Prus.  
  
  DEFAULTS: jobs = 4  
C:\TEMP> b2 --version  
Unable to load B2: could not find 'boost-build.jam'  
---------------------------------------------------  
Attempted search from 'c:\TEMP' up to the root at 'C:\b2\bin\b2.exe'  
Please consult the documentation at 'https://boostorg.github.io/build/'.  
```

---

## Comment 1

> Username: tee3  
> Created at: 2020-09-26 23:22:21 UTC  
> Url: https://github.com/boostorg/build/issues/650#issuecomment-699560094  

Thanks!  I had one question about the fix.  It seems you have changed the name of the project in most ways to b2 but have left the folder in "share" named "boost-build". Is that intentional?

---

## Comment 2

> Username: grafikrobot  
> Created at: 2020-09-27 02:11:02 UTC  
> Url: https://github.com/boostorg/build/issues/650#issuecomment-699573513  

It's intentional in that it's a temporary backward compatibility name. There's an alternate transportable install structure that implemented, but not default, that uses ".b2" as the name.

---

## Comment 3

> Username: tee3  
> Created at: 2020-09-27 12:56:42 UTC  
> Url: https://github.com/boostorg/build/issues/650#issuecomment-699632175  

Great, thanks for the explanation.  
  
> On Sep 26, 2020, at 10:11 PM, René Ferdinand Rivera Morell <notifications@github.com> wrote:  
>   
> ﻿  
> It's intentional in that it's a temporary backward compatibility name. There's an alternate transportable install structure that implemented, but not default, that uses ".b2" as the name.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub, or unsubscribe.
