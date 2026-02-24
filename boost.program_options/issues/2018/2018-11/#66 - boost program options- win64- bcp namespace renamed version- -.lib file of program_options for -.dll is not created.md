# #66 - boost program options: win64: bcp namespace renamed version: *.lib file of program_options for *.dll is not created [Open]

> Username: mrbachmann  
> Created at: 2018-11-20 15:50:55 UTC  
> Updated at: 2019-11-11 15:42:20 UTC  
> Url: https://github.com/boostorg/program_options/issues/66  

Between boost 1.63.0 and 1.65.1 line 12 of file 'libs/program_options/build/Jamfile.v2' has been changed:   
https://github.com/boostorg/program_options/commit/1f9413f532e58b82d12e9185c2eb8bc45612914c   
  
This change is the reason why the *.lib file for the program_options *.dll file is not created on win64/win32.  
  
Please consider reverting the change mentioned above partially and put back the definition of the flag BOOST_PROGRAM_OPTIONS_DYN_LINK=1:  
```  
diff -ruN boost_1_65_1.orig/libs/program_options/build/Jamfile.v2 boost_1_65_1/libs/program_options/build/Jamfile.v2  
--- boost_1_65_1.orig/libs/program_options/build/Jamfile.v2	2018-11-20 15:18:48.469951300 +0100  
+++ boost_1_65_1/libs/program_options/build/Jamfile.v2	2018-11-22 10:27:15.108147800 +0100  
@@ -11,6 +11,10 @@  
   
 boost-lib program_options  
     : $(SOURCES).cpp  
-    : # See https://svn.boost.org/trac/boost/ticket/5049  
+    : <link>shared:<define>BOOST_PROGRAM_OPTIONS_DYN_LINK=1 # tell source we're building dll's  
+      # See https://svn.boost.org/trac/boost/ticket/5049  
       <target-os>hpux,<toolset>gcc:<define>_INCLUDE_STDC__SOURCE_199901  
-    ;  
\ No newline at end of file  
+    :  
+    : <link>shared:<define>BOOST_PROGRAM_OPTIONS_DYN_LINK=1  
+    ;  
+  
```  
Similar to issue: https://github.com/boostorg/program_options/issues/60

---

## Comment 1

> Username: foundry-markf  
> Created at: 2019-11-07 17:12:59 UTC  
> Updated at: 2019-11-07 17:14:54 UTC  
> Url: https://github.com/boostorg/program_options/issues/66#issuecomment-551175229  

I have also encountered this problem in Boost 1.66.0 and I see the build scripts are unchanged in the develop branches.  
  
We are using bcp to namespace Boost. This rewrites the top-level Jamroot file, containing the `boost-lib` rule. This defines the shared #define to use the namespace specified in bcp, while the config.hpp in program_options continues to use BOOST as the prefix of the #define.  
  
It appears that program_options is the only module to use the `boost-lib` rule, and as such, is the one failing module in re-namespaced builds.  
  
I'm attaching a batch file that I've used in Windows to test a re-namespaced build in Boost 1.66.0 using bcp in case it's useful for those investigating.  
  
  
[build_npo.bat.txt](https://github.com/boostorg/program_options/files/3820869/build_npo.bat.txt)

---

## Comment 2

> Username: foundry-markf  
> Created at: 2019-11-11 15:42:20 UTC  
> Url: https://github.com/boostorg/program_options/issues/66#issuecomment-552496213  

After further investigation, I see someone fixed it up recently for 1.71.0 in https://github.com/boostorg/boost/commit/4a6094af6495a9cb944ae8f6de58e795a4910625  
  
I've tested that against the develop branch, and indeed, program_options is now behaving as expected in a bcp changed namespace build on Windows.
