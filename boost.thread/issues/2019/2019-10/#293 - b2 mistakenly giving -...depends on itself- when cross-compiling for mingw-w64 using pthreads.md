# #293 - b2 mistakenly giving "...depends on itself" when cross-compiling for mingw-w64 using pthreads. [Open]

> Username: Lord-Kamina  
> Created at: 2019-10-01 18:14:19 UTC  
> Updated at: 2019-10-01 18:16:09 UTC  
> Url: https://github.com/boostorg/thread/issues/293  

First, context: For the last couple of days I've been hacking at v1.71.0 to get it to cross-compile for mingw-w64 (From macOS) and using pthreads . Last night I was finally successful; but now am facing an odd error.  
  
I'm using [MXE](https://www.github.com/mxe/mxe), which installs shared .dll libraries in `<prefix>/bin`and then .dll.a files in `<prefix/lib>`  
  
One of the patches I made was the following:  
  
```diff  
diff --git a/libs/thread/build/Jamfile.v2 b/libs/thread/build/Jamfile.v2  
index 11111111..22222222 100644  
--- a/libs/thread/build/Jamfile.v2  
+++ b/libs/thread/build/Jamfile.v2  
@@ -165,6 +165,7 @@ rule win32_pthread_paths ( properties * )  
     local result ;  
     local PTW32_INCLUDE ;  
     local PTW32_LIB ;  
+    local libname ;  
     PTW32_INCLUDE  = [ modules.peek             : PTW32_INCLUDE ] ;  
     PTW32_LIB      = [ modules.peek             : PTW32_LIB     ] ;  
     PTW32_INCLUDE ?= [ modules.peek user-config : PTW32_INCLUDE ] ;  
@@ -192,14 +193,22 @@ rule win32_pthread_paths ( properties * )  
     {  
         local include_path = [ path.make $(PTW32_INCLUDE) ] ;  
         local lib_path = [ path.make $(PTW32_LIB) ] ;  
-        local libname = pthread ;  
+        if <link>shared in $(properties)  
+        {  
+            libname = pthread.dll ;  
+        }  
+        else  
+        {  
+            libname = pthread ;  
+        }  
+          
         if <toolset>msvc in $(properties)  
         {  
             libname = $(libname)VC2.lib ;  
         }  
         if <toolset>gcc in $(properties)  
         {  
-            libname = lib$(libname)GC2.a ;  
+            libname = lib$(libname).a ;  
         }  
         lib_path = [ path.glob $(lib_path) : $(libname) ] ;  
         if ! $(lib_path)  
  
```  
  
When passing `PTW32_INCLUDE`and `PTW32_LIB` arguments to b2; that finds the library and on a stage build everybody lives happily forever after; although I do get a warning that reads:  
  
`warning: <plibs/thread/src>/opt/mxe/usr/i686-w64-mingw32.shared/lib/libpthread.dll.a depends on itself`  
  
On a stage build that doesn't appear to be an issue; on an install build, this causes b2 to fail when installing Boost.Thread due to the following:  
  
```  
common.copy /opt/mxe/usr/i686-w64-mingw32.shared/lib/libpthread.dll.a  
  
    cp "/opt/mxe/usr/i686-w64-mingw32.shared/lib/libpthread.dll.a"  "/opt/mxe/usr/i686-w64-mingw32.shared/lib/libpthread.dll.a"  
  
cp: /opt/mxe/usr/i686-w64-mingw32.shared/lib/libpthread.dll.a and /opt/mxe/usr/i686-w64-mingw32.shared/lib/libpthread.dll.a are identical (not copied).  
...failed common.copy /opt/mxe/usr/i686-w64-mingw32.shared/lib/libpthread.dll.a...  
```  
and b2 explodes; but that's not all, because it then proceeds to `...removing /opt/mxe/usr/i686-w64-mingw32.shared/lib/libpthread.dll.a`  
  
And, if I hadn't had a recent backup, I essentially would have had to rebuild pretty much my entire environment because of that.  
  
I'm not sure if there's a way to get around this, or if anybody's got a better idea for how to deal with pthread inclusion; since apparently the Jamfile adding it as a `<library>` puts it into the same box as boost sources and causes this issue...  
  
Maybe the Jamfile should append to linkflags instead?  
  
P.S. I haven't yet tested my build but if it works I'll be more than happy to submit a PR with my modifications and/or eventually work to make them more acceptable so people can build against pthread of mingw-w64 without having to jump so many hoops.
