# #634 - Intel-linux C++11 flag is -std=c++11 [Closed]

> Username: aminiussi  
> Created at: 2020-07-08 09:48:54 UTC  
> Updated at: 2020-09-12 21:09:25 UTC  
> Closed at: 2020-09-12 21:09:24 UTC  
> Url: https://github.com/boostorg/build/issues/634  

Hi,  
  
With icc/icpc, the c++11 selection flag is the same as g++: -std=c++11.  
Right now -xc++ is used in two places of build.sh:  
  
```  
[alainm@castor engine]$ git diff  
diff --git a/src/engine/build.sh b/src/engine/build.sh  
index 41af371..86e1065 100755  
--- a/src/engine/build.sh  
+++ b/src/engine/build.sh  
@@ -81,7 +81,7 @@ test_cxx11 ()  
         case $1 in  
             gcc) ( ${CXX:=g++} -x c++ -std=c++11 check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;  
             intel-darwin) ( ${CXX:=icc} -xc++ check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;  
-            intel-linux) ( ${CXX:=icc} -xc++ check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;  
+            intel-linux) ( ${CXX:=icc} -std=c++11 check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;  
             vacpp) ( ${CXX:=xlC_r} check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;  
             xlcpp) ( ${CXX:=xlC_r} check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;  
             como) ( ${CXX:=como} check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;  
@@ -283,7 +283,7 @@ case $B2_TOOLSET in  
             export LD_RUN_PATH  
             . ${B2_TOOLSET_ROOT}bin/iccvars.sh $ARCH  
         fi  
-        B2_CXX="${CXX} -xc++"  
+        B2_CXX="${CXX} -std=c++11"  
         B2_CXXFLAGS_RELEASE="-O3 -s"  
         B2_CXXFLAGS_DEBUG="-O0 -g -p"  
     ;;  
[alainm@castor engine]$  git status  
# On branch master  
# Changes not staged for commit:  
#   (use "git add <file>..." to update what will be committed)  
#   (use "git checkout -- <file>..." to discard changes in working directory)  
#  
#	modified:   build.sh  
#  
no changes added to commit (use "git add" and/or "git commit -a")  
[alainm@castor engine]$  
```

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-09-12 21:09:24 UTC  
> Url: https://github.com/boostorg/build/issues/634#issuecomment-691548935  

Resolved with https://github.com/boostorg/build/commit/86271a42865b2d6ebf4440549e9df1ccc05e9d13
