# #385 - Bjam error when building for Android from Windows with Boost 1.69 (working with 1.68 and earlier) [Closed]

> Username: Eoinocal  
> Created at: 2019-01-08 14:23:36 UTC  
> Updated at: 2022-05-02 12:43:02 UTC  
> Closed at: 2022-05-02 12:43:02 UTC  
> Url: https://github.com/boostorg/build/issues/385  

With Boost 1.68 and earlier I would build static libraries for Android arm64 with these three steps.  
  
## Step 1  
Run `bootstrap.bat` and add the following to `project-config.jam`. Note `androidNDKRoot` points to where I've installed the NDK Bundle.  
  
    androidNDKRoot = /Craftwork/Android/sdk/ndk-bundle ;  
      
    using clang : arm64   
    :  
        $(androidNDKRoot)/toolchains/llvm/prebuilt/windows-x86_64/bin/clang++.exe    
    :  
        <compileflags>--sysroot=$(androidNDKRoot)/sysroot   
        <compileflags>-I$(androidNDKRoot)/sources/cxx-stl/llvm-libc++/include   
        <compileflags>-I$(androidNDKRoot)/sources/cxx-stl/llvm-libc++abi/include   
        <compileflags>-I$(androidNDKRoot)/sources/android/support/include   
        <compileflags>-I$(androidNDKRoot)/sysroot/usr/include/aarch64-linux-android   
        <compileflags>-g   
        <compileflags>-O3  
        <cxxflags>-std=c++14  
        <compileflags>--target=aarch64-arm-linux-androideabi   
    ;  
  
## Step 2  
  
Add  
  
    C:\Craftwork\Android\sdk\ndk-bundle\toolchains\aarch64-linux-android-4.9\prebuilt\windows-x86_64\aarch64-linux-android\bin  
  
to `PATH`. This is to workaround [ [Boost-build] How to make clang toolset use custom archiver and ranlib from user-config.jam? - "ar and ranlib cannot be customized for clang toolset"](https://lists.boost.org/boost-build/2017/03/29331.php).  
  
## Step 3  
  
Run `b2 target-os=android toolset=clang-arm64 link=static --with-system`  
  
Note `--with-system` is just for quicker testing and reproduction of the error.  
  
## Result  
  
This process works fine for 1.68 and many earlier versions. But with 1.69 I get the following error before compilation starts.  
  
    C:\Craftwork\boost_1_69_0>b2 target-os=android toolset=clang-arm64 link=static --with-system  
    Performing configuration checks  
      
        - default address-model    : 64-bit  
        - default architecture     : arm  
      
    Building the Boost C++ Libraries.  
      
      
    C:/Craftwork/boost_1_69_0/tools/build/src/tools\common.jam:979: in toolset-tag  
    *** argument error  
    * rule numbers.less ( n1 n2 )  
    * called with: ( 3 )  
    * missing argument n2  
    C:/Craftwork/boost_1_69_0/tools/build/src/util\numbers.jam:66:see definition of rule 'numbers.less' being called  
    C:/Craftwork/boost_1_69_0/tools/build/src/tools\common.jam:850: in common.format-name  
    C:/Craftwork/boost_1_69_0\boostcpp.jam:188: in boostcpp.tag  
    Jamroot:191: in Jamfile<C:\Craftwork\boost_1_69_0>.tag  
    C:/Craftwork/boost_1_69_0/tools/build/src/kernel\modules.jam:107: in modules.call-in  
    C:/Craftwork/boost_1_69_0/tools/build/src/util\indirect.jam:105: in indirect.call  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\virtual-target.jam:476: in _adjust-name  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\virtual-target.jam:274: in abstract-file-target.__init__  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\virtual-target.jam:582: in class@file-target.__init__  
    C:/Craftwork/boost_1_69_0/tools/build/src/kernel\class.jam:90: in class.new  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\generators.jam:576: in generated-targets  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\generators.jam:474: in construct-result  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\generators.jam:418: in run-really  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\generators.jam:392: in generator.run  
    C:/Craftwork/boost_1_69_0/tools/build/src/tools/generators\archive-generator.jam:32: in archive-generator.run  
    C:/Craftwork/boost_1_69_0/tools/build/src/tools\unix.jam:87: in class@unix-archive-generator.run  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\generators.jam:1019: in try-one-generator-really  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\generators.jam:1081: in try-one-generator  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\generators.jam:1319: in construct-really  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\generators.jam:1405: in generators.construct  
    C:/Craftwork/boost_1_69_0/tools/build/src/tools/generators\lib-generator.jam:59: in class@lib-generator.run  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\generators.jam:1019: in try-one-generator-really  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\generators.jam:1081: in try-one-generator  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\generators.jam:1319: in construct-really  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\generators.jam:1405: in generators.construct  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:1614: in construct  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:1363: in class@basic-target.generate  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:812: in generate-really  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:784: in class@main-target.generate  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:273: in class@project-target.generate  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:933: in targets.generate-from-reference  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:1276: in generate-dependencies  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:1333: in class@basic-target.generate  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:812: in generate-really  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:784: in class@main-target.generate  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:933: in targets.generate-from-reference  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:1276: in generate-dependencies  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:1333: in class@basic-target.generate  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:812: in generate-really  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:784: in class@main-target.generate  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:933: in targets.generate-from-reference  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:1276: in generate-dependencies  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:1333: in alias-target-class.generate  
    C:/Craftwork/boost_1_69_0\boostcpp.jam:480: in build-multiple  
    C:/Craftwork/boost_1_69_0\boostcpp.jam:470: in class@top-level-target.generate  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:812: in generate-really  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:784: in class@main-target.generate  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:933: in targets.generate-from-reference  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:1276: in generate-dependencies  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:1333: in alias-target-class.generate  
    C:/Craftwork/boost_1_69_0\boostcpp.jam:480: in build-multiple  
    C:/Craftwork/boost_1_69_0\boostcpp.jam:470: in class@top-level-target.generate  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:812: in generate-really  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:784: in class@main-target.generate  
    C:/Craftwork/boost_1_69_0/tools/build/src/build\targets.jam:273: in class@project-target.generate  
    C:/Craftwork/boost_1_69_0/tools/build/src\build-system.jam:797: in load  
    C:\Craftwork\boost_1_69_0\tools\build\src/kernel\modules.jam:295: in import  
    C:\Craftwork\boost_1_69_0\tools\build\src/kernel/bootstrap.jam:139: in boost-build  
    C:\Craftwork\boost_1_69_0\boost-build.jam:17: in module scope  
      
    C:\Craftwork\boost_1_69_0>

---

## Comment 1

> Username: Eoinocal  
> Created at: 2019-01-20 21:07:14 UTC  
> Url: https://github.com/boostorg/build/issues/385#issuecomment-455902431  

As a workaround open the mentioned file `boost_1_69_0/tools/build/src/tools\common.jam` and starting at line 979, comment out the 4 line `if` block like this  
  
        # Ditto, from Clang 4  
    #    if $(tag) in clang clangw && [ numbers.less 3 $(version[1]) ]  
    #    {  
    #        version = $(version[1]) ;  
    #    }

---

## Comment 2

> Username: Eoinocal  
> Created at: 2019-01-22 23:27:48 UTC  
> Url: https://github.com/boostorg/build/issues/385#issuecomment-456605498  

Sorry, I accidentally closed this when adding the workaround.

---

## Comment 3

> Username: dec1  
> Created at: 2019-04-22 14:33:35 UTC  
> Updated at: 2019-04-22 14:35:06 UTC  
> Url: https://github.com/boostorg/build/issues/385#issuecomment-485434589  

I also have this problem when building for android (on kununtu 18.04 host), as of boost version 1.70.0, with clang

---

## Comment 4

> Username: dec1  
> Created at: 2019-04-22 14:34:56 UTC  
> Updated at: 2019-04-22 15:40:02 UTC  
> Url: https://github.com/boostorg/build/issues/385#issuecomment-485435009  

> As a workaround open the mentioned file `boost_1_69_0/tools/build/src/tools\common.jam` and starting at line 979, comment out the 4 line `if` block like this  
>   
> ```  
>     # Ditto, from Clang 4  
> #    if $(tag) in clang clangw && [ numbers.less 3 $(version[1]) ]  
> #    {  
> #        version = $(version[1]) ;  
> #    }  
> ```  
  
Commenting out this also ws a workaround for me. Seems clang version is empty.  
  
Here is the clang version Im using:  
```  
> clang --version  
clang version 6.0.0-1ubuntu2 (tags/RELEASE_600/final)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
```  
  
Can be reproduced with the following [Dockerfile](https://github.com/dec1/Boost-for-Android/blob/master/docker/Dockerfile)

---

## Comment 5

> Username: rcdailey  
> Created at: 2019-06-19 15:30:56 UTC  
> Url: https://github.com/boostorg/build/issues/385#issuecomment-503611176  

FYI, This issue still exists in Boost v1.70. I created this patch which, for me, fixes the problem (cross compiling using Android NDK).  
  
```patch  
diff --git src/tools/common.jam src/tools/common.jam  
index 5b9b3508..b399916c 100644  
--- src/tools/common.jam  
+++ src/tools/common.jam  
@@ -974,16 +974,16 @@ local rule toolset-tag ( name : type ? : property-set )  
     }  
   
     # From GCC 5, versioning changes and minor becomes patch  
-    if $(tag) = gcc && [ numbers.less 4 $(version[1]) ]  
-    {  
-        version = $(version[1]) ;  
-    }  
-  
-    # Ditto, from Clang 4  
-    if ( $(tag) = clang || $(tag) = clangw ) && [ numbers.less 3 $(version[1]) ]  
-    {  
-        version = $(version[1]) ;  
-    }  
+    # if $(tag) = gcc && [ numbers.less 4 $(version[1]) ]  
+    # {  
+    #     version = $(version[1]) ;  
+    # }  
+  
+    # # Ditto, from Clang 4  
+    # if ( $(tag) = clang || $(tag) = clangw ) && [ numbers.less 3 $(version[1]) ]  
+    # {  
+    #     version = $(version[1]) ;  
+    # }  
   
     # On intel, version is not added, because it does not matter and it is the  
     # version of vc used as backend that matters. Ideally, we should encode the  
```

---

## Comment 6

> Username: Haffon  
> Created at: 2019-08-20 11:48:51 UTC  
> Url: https://github.com/boostorg/build/issues/385#issuecomment-522977851  

This issue still exists in Boost v1.71.0

---

## Comment 7

> Username: stale[bot]  
> Created at: 2021-06-11 01:54:46 UTC  
> Url: https://github.com/boostorg/build/issues/385#issuecomment-859203218  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 8

> Username: floli  
> Created at: 2022-05-02 09:42:18 UTC  
> Url: https://github.com/boostorg/build/issues/385#issuecomment-1114671713  

Anyone have a link on that issue in the b2 repo?

---

## Comment 9

> Username: grafikrobot  
> Created at: 2022-05-02 12:43:02 UTC  
> Url: https://github.com/boostorg/build/issues/385#issuecomment-1114803278  

@floli no one has posted a corresponding issue in the b2 repo.
