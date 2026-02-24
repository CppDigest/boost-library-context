# #331 - Missing dependency on scope_exit with CMake build [Open]

> Username: aminiussi  
> Created at: 2023-08-02 10:52:21 UTC  
> Updated at: 2023-08-02 10:52:21 UTC  
> Url: https://github.com/boostorg/process/issues/331  

As the lib include scoped_exit header, there should be a dependency:  
```  
12:49:50 [alainm@castor process]# git diff -c  
diff --git a/CMakeLists.txt b/CMakeLists.txt  
index c0c1bfe..58162fb 100644  
--- a/CMakeLists.txt  
+++ b/CMakeLists.txt  
@@ -22,6 +22,7 @@ target_link_libraries(boost_process  
     Boost::iterator  
     Boost::move  
     Boost::optional  
+    Boost::scope_exit  
     Boost::system  
     Boost::tokenizer  
     Boost::type_index  
12:49:53 [alainm@castor process]#  
```  
thx
