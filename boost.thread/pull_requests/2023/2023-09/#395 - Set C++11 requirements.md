# #395 Set C++11 requirements [Closed]

> Username: Flamefire  
> Created at: 2023-09-12 07:18:47 UTC  
> Updated at: 2024-12-09 10:29:35 UTC  
> Closed at: 2024-12-09 10:29:34 UTC  
> Url: https://github.com/boostorg/thread/pull/395  

Due to lexical_cast the library requires C++11 which it doesn't advertise making dependents fail in C++03 CI  
  
This sets the requirements manually (atomic and lexical_cast is [used] header-only so we don't get them automatically)  
  
I had an alternative which I want to mention here which basically removes the dependency on lexical_cast:  
  
```  
diff --git a/CMakeLists.txt b/CMakeLists.txt  
index d724ea36..38c137e8 100644  
--- a/CMakeLists.txt  
+++ b/CMakeLists.txt  
@@ -83,7 +83,6 @@ target_link_libraries(boost_thread  
   
   PRIVATE  
     Boost::algorithm  
-    Boost::lexical_cast  
 )  
   
 target_compile_definitions(boost_thread  
diff --git a/src/pthread/thread.cpp b/src/pthread/thread.cpp  
index c17aca2c..6d75a5ec 100644  
--- a/src/pthread/thread.cpp  
+++ b/src/pthread/thread.cpp  
@@ -35,9 +35,9 @@  
   
 #include <boost/algorithm/string/split.hpp>  
 #include <boost/algorithm/string/trim.hpp>  
-#include <boost/lexical_cast.hpp>  
   
 #include <fstream>  
+#include <sstream>  
 #include <string>  
 #include <set>  
 #include <vector>  
@@ -47,6 +47,17 @@ namespace boost  
 {  
     namespace detail  
     {  
+        template<typename Target>  
+        Target classic_convert(const std::string& src)  
+        {  
+            std::istringstream s(src);  
+            s.imbue(std::locale::classic());  
+            Target res;  
+            if(s >> res)  
+                return res;  
+            throw std::bad_cast("Cannot convert " + src);  
+        }  
+  
         thread_data_base::~thread_data_base()  
         {  
             for (notify_list_t::iterator i = notify.begin(), e = notify.end();  
@@ -547,12 +558,12 @@ namespace boost  
                 boost::trim(value);  
   
                 if (key == physical_id) {  
-                    current_core_entry.first = boost::lexical_cast<unsigned>(value);  
+                    current_core_entry.first = classic_cast<unsigned>(value);  
                     continue;  
                 }  
   
                 if (key == core_id) {  
-                    current_core_entry.second = boost::lexical_cast<unsigned>(value);  
+                    current_core_entry.second = classic_cast<unsigned>(value);  
                     cores.insert(current_core_entry);  
                     continue;  
```  
  
Might be worth a consideration as I'm not sure `lexical_cast` (IIRC localized conversion) is correct here.

---
