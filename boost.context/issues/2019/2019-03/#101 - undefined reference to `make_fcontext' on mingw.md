# #101 - undefined reference to `make_fcontext' on mingw [Closed]

> Username: jschueller  
> Created at: 2019-03-08 16:20:12 UTC  
> Updated at: 2020-04-24 09:04:32 UTC  
> Closed at: 2020-04-24 09:03:30 UTC  
> Url: https://github.com/boostorg/context/issues/101  

on mingw (Fedora 29, gcc 8.3.0) boost.context builds successfuly, but has undefined symbols, here on the fibonnacci example  
  
```  
$ x86_64-w64-mingw32-g++ fibonacci.cpp -lboost_context-mt  
... undefined reference to `make_fcontext'  
... undefined reference to `jump_fcontext'  
... undefined reference to `jump_fcontext'  
... undefined reference to `ontop_fcontext'  
...  
collect2: error: ld returned 1 exit status  
```  
  
But, if built with the fedora patch, all symbols are resolved and the build suceeds (but returns an exception, see #100):  
```  
--- libs/context/build/Jamfile.v2.orig	2013-03-03 13:39:59.684868916 +0100  
+++ libs/context/build/Jamfile.v2	2013-03-03 15:09:09.893232829 +0100  
@@ -47,10 +47,11 @@  
 local rule default_binary_format ( )  
 {  
     local tmp = elf ;  
-    if [ os.name ] = "NT" { tmp = pe ; }  
-    else if [ os.name ] = "CYGWIN" { tmp = pe ; }  
-    else if [ os.name ] = "AIX" { tmp = xcoff ; }  
-    else if [ os.name ] = "MACOSX" { tmp = mach-o ; }  
+    #if [ os.name ] = "NT" { tmp = pe ; }  
+    #else if [ os.name ] = "CYGWIN" { tmp = pe ; }  
+    #else if [ os.name ] = "AIX" { tmp = xcoff ; }  
+    #else if [ os.name ] = "MACOSX" { tmp = mach-o ; }  
+    tmp = pe ;  
     return $(tmp) ;  
 }  
   
@@ -67,10 +68,11 @@  
 local rule default_abi ( )  
 {  
     local tmp = sysv ;  
-    if [ os.name ] = "NT" { tmp = ms ; }  
-    else if [ os.name ] = "CYGWIN" { tmp = ms ; }  
-    else if [ os.platform ] = "ARM" { tmp = aapcs ; }  
-    else if [ os.platform ] = "MIPS" { tmp = o32 ; }  
+    #if [ os.name ] = "NT" { tmp = ms ; }  
+    #else if [ os.name ] = "CYGWIN" { tmp = ms ; }  
+    #else if [ os.platform ] = "ARM" { tmp = aapcs ; }  
+    #else if [ os.platform ] = "MIPS" { tmp = o32 ; }  
+    tmp = ms ;  
     return $(tmp) ;  
 }  
 ```  
  
I guess boost.context doesnt know how to detect this format/abi when compiling from Linux with a gcc compiler for the windows target (pe binary format, ms abi), is this a known issue ?  
  
Link to the fedora rpm (for 1.66 but their patch is still a valid workaround for 1.69, did they report that bug to you ?):  
https://www.rpmfind.net/linux/RPM/fedora/devel/rawhide/ppc64le/m/mingw64-boost-1.66.0-3.fc30.noarch.html

---

## Comment 1

> Username: olk  
> Created at: 2020-04-24 09:03:30 UTC  
> Updated at: 2020-04-24 09:04:32 UTC  
> Url: https://github.com/boostorg/context/issues/101#issuecomment-618894819  

outdated + the fedora patch simply comments out all other platforms -> this is not a valid patch  
closing
