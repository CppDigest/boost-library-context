# #448 - configure.builds and check-target-builds always build in debug mode [Open]

> Username: ledocc  
> Created at: 2019-06-03 12:48:14 UTC  
> Updated at: 2021-05-29 18:22:31 UTC  
> Url: https://github.com/boostorg/build/issues/448  

posted on boost.user mailing list 9 days ago, but no answerer so I try here ...   
  
Hi,  
  
I try to add icu support in boost conan package, but on windows with msvc, the build never use icu.  
  
During icu detection in boost.locale, the has_icu.exe never build because configure.builds try to build in debug mode when I use property "variant=release" in b2 command line. I can see that in config.log written by b2 during build : b2 use icuucd.lib and icuind.lib instead of icuuc.lib and icuin.lib. So icu is not correctly detected, and so never used. If I bypass the has_icu test, boost locale build fine and have icu backend as expected.  
  
This behaviors only fail builds that use different library in release and debug mode, like icu build with msvc.  
  
Is it the correct behaviors ?  
 - If yes this is a bad news for library with different library name in release and debug mode.  
 - If no ... houston, we have a problem ... Can you give me some hint to fix this?  
  
Best regards

---

## Comment 1

> Username: ledocc  
> Created at: 2019-10-16 04:54:52 UTC  
> Url: https://github.com/boostorg/build/issues/448#issuecomment-542514007  

up

---

## Comment 2

> Username: ledocc  
> Created at: 2019-10-28 09:54:36 UTC  
> Url: https://github.com/boostorg/build/issues/448#issuecomment-546872662  

The solution is to add <variant> <link> <runtime-link> <define> to relevants features of "configure" module by calling   
`feature.compose <configure> : <variant> <link> <runtime-link> <define> ;`  
as suggested in src/build/configure.jam. But when I do this, an error occur:  
  
> source/boost/tools/build/src/build/feature.jam:690: in feature.compose from module feature  
error: components of <configure> already set: <target-os> <toolset> <address-model> <architecture> <cxxstd>  
source/boost/tools/build/src/build/configure.jam:45: in load from module configure  
source/boost/tools/build/src/kernel/modules.jam:295: in import from module modules  
source/boost/tools/build/src/build-system.jam:14: in load from module build-system  
source/boost/tools/build/src/kernel/modules.jam:295: in import from module modules  
source/boost/tools/build/src/kernel/bootstrap.jam:139: in boost-build from module  
source/boost/boost-build.jam:17: in module scope from module  
  
I found no way to add feature to "configure" module.

---

## Comment 3

> Username: orangejuicy  
> Created at: 2020-01-10 19:58:13 UTC  
> Url: https://github.com/boostorg/build/issues/448#issuecomment-573183428  

I think, I can fix this with patch:  
```  
diff --git a/tools/build/src/build/configure.jam b/tools/build/src/build/configure.jam  
index f9df172b..29c4884e 100644  
--- a/tools/build/src/build/configure.jam  
+++ b/tools/build/src/build/configure.jam  
@@ -39,7 +39,7 @@ feature.feature configure : : composite optional ;  
 # relevant features are also considered relevant.  
 #  
 feature.compose <configure> :  
-    <target-os> <toolset> <address-model> <architecture> <cxxstd> ;  
+    <target-os> <variant> <toolset> <address-model> <architecture> <cxxstd> ;  
```

---

## Comment 4

> Username: ledocc  
> Created at: 2020-01-13 11:23:14 UTC  
> Url: https://github.com/boostorg/build/issues/448#issuecomment-573617413  

I @orangejuicy, Here the patch I use on my side for now  
```  
--- a/tools/build/src/build/configure.jam       2019-08-14 14:03:50.000000000 +0200  
+++ b/tools/build/src/build/configure.jam       2019-09-10 14:27:54.377625059 +0200  
@@ -41,4 +41,4 @@  
 # relevant features are also considered relevant.  
 #  
 feature.compose <configure> :  
-    <target-os> <toolset> <address-model> <architecture> <cxxstd> ;  
+    <target-os> <toolset> <address-model> <architecture> <cxxstd> <variant> <link> <runtime-link> <define> ;  
  
  
 rule log-summary ( )  
```  
to properly build has_icu program test, we also need "link", "runtime-link", and "define" options

---

## Comment 5

> Username: ledocc  
> Created at: 2020-02-20 10:20:33 UTC  
> Url: https://github.com/boostorg/build/issues/448#issuecomment-588908351  

up

---

## Comment 6

> Username: stale[bot]  
> Created at: 2021-05-29 18:22:01 UTC  
> Url: https://github.com/boostorg/build/issues/448#issuecomment-850877312  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
