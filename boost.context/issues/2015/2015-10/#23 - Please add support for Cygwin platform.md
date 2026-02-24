# #23 - Please add support for Cygwin platform [Closed]

> Username: lynnboy  
> Created at: 2015-10-08 03:40:42 UTC  
> Updated at: 2015-10-11 06:58:50 UTC  
> Closed at: 2015-10-11 06:58:50 UTC  
> Url: https://github.com/boostorg/context/issues/23  

Let's apply a couple of lines:  
  
``` diff  
$ git diff build/Jamfile.v2  
diff --git a/build/Jamfile.v2 b/build/Jamfile.v2  
index af8de28..855fd35 100644  
--- a/build/Jamfile.v2  
+++ b/build/Jamfile.v2  
@@ -47,6 +47,7 @@ local rule default_binary_format ( )  
     local tmp = elf ;  
     if [ os.name ] = "MACOSX" { tmp = mach-o ; }  
     if [ os.name ] = "NT" { tmp = pe ; }  
+    if [ os.name ] = "CYGWIN" { tmp = pe ; }  
     if [ os.name ] = "AIX" { tmp = xcoff ; }  
     return $(tmp) ;  
 }  
@@ -65,6 +66,7 @@ local rule default_abi ( )  
 {  
     local tmp = sysv ;  
     if [ os.name ] = "NT" { tmp = ms ; }  
+    if [ os.name ] = "CYGWIN" { tmp = ms ; }  
     else if [ os.platform ] = "ARM" { tmp = aapcs ; }  
     else if [ os.platform ] = "MIPS" { tmp = o32 ; }  
     return $(tmp) ;  
```

---

## Comment 1

> Username: olk  
> Created at: 2015-10-08 04:07:54 UTC  
> Url: https://github.com/boostorg/context/issues/23#issuecomment-146414363  

please provide a patch and run the unit-tests (!)

---

## Comment 2

> Username: lynnboy  
> Created at: 2015-10-09 06:46:43 UTC  
> Url: https://github.com/boostorg/context/issues/23#issuecomment-146771090  

Sorry, I've run `boost/context/test/` and both tests are passed.  
  
I failed to attach a file in a github issue (what's wrong with github?), but the total change is the specified two lines.  
  
The `os.name` reported for Cygwin is different than both MSVC and MinGW, but their binary format and ABI are just the same.

---

## Comment 3

> Username: olk  
> Created at: 2015-10-11 06:58:50 UTC  
> Url: https://github.com/boostorg/context/issues/23#issuecomment-147164688  

done
