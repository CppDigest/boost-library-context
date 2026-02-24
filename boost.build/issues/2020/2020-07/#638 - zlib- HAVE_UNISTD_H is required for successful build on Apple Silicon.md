# #638 - zlib: HAVE_UNISTD_H is required for successful build on Apple Silicon [Open]

> Username: k15tfu  
> Created at: 2020-07-13 20:04:46 UTC  
> Updated at: 2022-06-14 15:17:24 UTC  
> Url: https://github.com/boostorg/build/issues/638  

^^  
  
Otherwise, some functions will be undefined and zlib devs insist that there are no errors when building with `./configure` (https://github.com/madler/zlib/pull/509). So from this point of view we have to pass the right flags if we don't use this script.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-09-12 21:03:40 UTC  
> Url: https://github.com/boostorg/build/issues/638#issuecomment-691547968  

This is totally unrelated to b2.

---

## Comment 2

> Username: k15tfu  
> Created at: 2020-11-30 15:01:41 UTC  
> Url: https://github.com/boostorg/build/issues/638#issuecomment-735839013  

@grafikrobot Why? The code you use for building zlib is incorrect because `HAVE_UNISTD_H` define is missing: https://github.com/boostorg/build/blob/003a3c29c12427c5a424f2332aa4ba00a8554a88/src/tools/zlib.jam#L203

---

## Comment 3

> Username: grafikrobot  
> Created at: 2020-11-30 15:27:05 UTC  
> Url: https://github.com/boostorg/build/issues/638#issuecomment-735855101  

@k15tfu ah, I see. Thanks for the clarification.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:40 UTC  
> Url: https://github.com/boostorg/build/issues/638#issuecomment-850859643  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 5

> Username: k15tfu  
> Created at: 2022-06-14 15:17:24 UTC  
> Url: https://github.com/boostorg/build/issues/638#issuecomment-1155341464  

@grafikrobot Hi! Is there anything I can help with? Locally we use this patch to get it to build on Apple Silicon, and I can make a PR for this:  
```  
% diff -uar ./tools/build/src/tools/zlib.jam.orig ./tools/build/src/tools/zlib.jam  
--- ./tools/build/src/tools/zlib.jam    2020-07-13 22:21:52.000000000 +0200  
+++ ./tools/build/src/tools/zlib.jam    2020-07-13 22:21:13.000000000 +0200  
@@ -200,6 +200,7 @@  
                 <include>$(source-path)  
                 <toolset>msvc:<define>_CRT_SECURE_NO_DEPRECATE  
                 <toolset>msvc:<define>_SCL_SECURE_NO_DEPRECATE  
+                <toolset>clang:<define>HAVE_UNISTD_H=1  
                 <link>shared:<define>ZLIB_DLL  
               :  
               : <include>$(source-path) ] ;```
