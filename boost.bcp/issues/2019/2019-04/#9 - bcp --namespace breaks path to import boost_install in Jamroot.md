# #9 - bcp --namespace breaks path to import boost_install in Jamroot [Open]

> Username: jbmercha  
> Created at: 2019-04-19 19:55:14 UTC  
> Updated at: 2024-10-13 17:14:57 UTC  
> Url: https://github.com/boostorg/bcp/issues/9  

When using bcp.exe to create a boost subset with a new namespace, building the subset is broken because in the Jamfile the import for boost_install has had the "boost_" portion renamed to the new namespace.  
  
```  
Build\boost>.\dist\bin\bcp.exe --namespace=myboost --namespace-alias regex log headers predef config build boost_install ..\foo  
Build\boost>cd ..\foo  
Build\foo>.\bootstrap.bat  
Build\foo>.\b2 toolset=msvc-14.1 --with-regex --with-log  
boost-install.jam: No such file or directory  
Jamroot:307: in boost-install  
ERROR: rule "boost-install.boost-install" unknown in module "Jamfile<Build\foo>".  
libs\atomic\build\Jamfile.v2:38: in modules.load  
```

---

## Comment 1

> Username: firewave  
> Created at: 2019-06-17 09:25:15 UTC  
> Updated at: 2019-06-17 09:49:03 UTC  
> Url: https://github.com/boostorg/bcp/issues/9#issuecomment-502606201  

This issue was introduced with Boost 1.70 and prevents us from updating.  
  
@pdimov After looking at the commit log I suspect https://github.com/boostorg/boost_install/commit/90b1f04f4485c06f907ecba6a92b7144bf211014 might be the cause.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-06-17 10:05:14 UTC  
> Url: https://github.com/boostorg/bcp/issues/9#issuecomment-502620046  

This should be fixed with https://github.com/boostorg/boost/commit/d0586e88122f41cd5ac9666db70c37d6f0fc7480.

---

## Comment 3

> Username: firewave  
> Created at: 2019-06-17 11:23:52 UTC  
> Url: https://github.com/boostorg/bcp/issues/9#issuecomment-502642956  

Thanks. I applied the patch locally but I still get the same error.

---

## Comment 4

> Username: pdimov  
> Created at: 2019-06-17 11:53:19 UTC  
> Url: https://github.com/boostorg/bcp/issues/9#issuecomment-502651295  

What command are you using? Note that without https://github.com/boostorg/bcp/pull/5, you need to manually add `headers` and `boost_install` to the `bcp` command.

---

## Comment 5

> Username: firewave  
> Created at: 2019-06-17 11:58:16 UTC  
> Url: https://github.com/boostorg/bcp/issues/9#issuecomment-502652818  

I am using https://github.com/conan-community/conan-boost with `boost:namespace = boost_ns` and `boost:namespace_alias = True`. I will see if I can find the actual commands being used in the output.

---

## Comment 6

> Username: pdimov  
> Created at: 2019-06-17 12:03:33 UTC  
> Url: https://github.com/boostorg/bcp/issues/9#issuecomment-502654437  

Looks like you need to either add `headers` and `boost_install` to https://github.com/conan-community/conan-boost/blob/release/1.70.0/conanfile.py#L366, or patch `bcp` as per the above PR.

---

## Comment 7

> Username: firewave  
> Created at: 2019-06-17 13:25:12 UTC  
> Updated at: 2019-06-17 13:25:33 UTC  
> Url: https://github.com/boostorg/bcp/issues/9#issuecomment-502680942  

Thanks a lot - that seems to have done it (I added the libraries).  
  
I will open a ticket with the boost-conan project so they are aware.

---

## Comment 8

> Username: adesitter  
> Created at: 2024-10-13 14:17:32 UTC  
> Updated at: 2024-10-13 14:19:07 UTC  
> Url: https://github.com/boostorg/bcp/issues/9#issuecomment-2408997754  

The issue has re-appeared with boost 1.86.  
```  
mv boost_1_86_0 fullboost  
mkdir boost_1_86_0  
cd fullboost  
  
mv boost_1_86_0 fullboost  
mkdir boost_1_86_0  
cd fullboost  
./bootstrap.sh --with-toolset=gcc --with-libraries=chrono,date_time,iostreams,math,program_options,system,thread  
./b2 linkflags="-static-libstdc++" tools/bcp  
dist/bin/bcp --namespace=xxxboost  build boost_install chrono date_time iostreams math program_options system thread spirit numeric crc.hpp random.hpp ../boost_1_86_0  
```  
fails to build.  
  
Two patches are necessary to make it work.  
#1 [boost_bcp_1.patch] replace removed method of boost::filesystem.   
This is https://github.com/boostorg/boost/issues/942.  
The fixes have already been committed to https://github.com/boostorg/bcp.  
  
#2 [boost_bcp_2.patch] fix management of "boost_install" in Jamroot  
This is similar to https://github.com/boostorg/boost/commit/4a6094af6495a9cb944ae8f6de58e795a4910625 authored by @pdimov.  
  
The second patch is:  
```  
diff -ru orig/boost_1_86_0/Jamroot new/boost_1_86_0/Jamroot  
--- orig/boost_1_86_0/Jamroot	2024-08-07 22:07:32.000000000 +0000  
+++ new/boost_1_86_0/Jamroot	2024-09-06 19:29:05.931827578 +0000  
@@ -461,7 +461,7 @@  
     project.pop-current ;  
 }  
   
-if ! [ project.search /boost/tools/boost_install ]  
+if ! [ project.search /boost/tools/boost\_install ]  
 {  
-    use-project /boost/tools/boost_install : tools/boost_install ;  
+    use-project /boost/tools/boost\_install : tools/boost\_install ;  
 }  
```  
[boost_bcp_1.patch](https://github.com/user-attachments/files/17355923/boost_bcp_1.patch)  
[boost_bcp_2.patch](https://github.com/user-attachments/files/17355924/boost_bcp_2.patch)

---

## Comment 9

> Username: pdimov  
> Created at: 2024-10-13 17:14:56 UTC  
> Url: https://github.com/boostorg/bcp/issues/9#issuecomment-2409055726  

I've fixed Jamroot again.
