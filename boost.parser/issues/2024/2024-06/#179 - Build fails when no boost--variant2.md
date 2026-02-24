# #179 - Build fails when no boost::variant2 [Closed]

> Username: isominsky  
> Created at: 2024-06-11 20:15:25 UTC  
> Updated at: 2024-09-30 22:44:29 UTC  
> Closed at: 2024-09-30 22:44:29 UTC  
> Url: https://github.com/boostorg/parser/issues/179  

diff --git a/test/tuple_aggregate.cpp b/test/tuple_aggregate.cpp  
index fc6af6f7..3effce66 100644  
--- a/test/tuple_aggregate.cpp  
+++ b/test/tuple_aggregate.cpp  
@@ -81,7 +81,7 @@ struct s1_boost_variant  
     std::vector<int> vec_;  
 };  
 #endif  
-#if TEST_BOOST_VARIANT  
+#if TEST_BOOST_VARIANT2  
 struct s1_boost_variant2  
 {  
     int i_;

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-09-29 20:42:23 UTC  
> Url: https://github.com/boostorg/parser/issues/179#issuecomment-2381595852  

Thanks!  Classic copy pasta.
