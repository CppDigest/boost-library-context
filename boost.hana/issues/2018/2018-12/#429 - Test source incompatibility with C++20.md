# #429 - Test source incompatibility with C++20 [Closed]

> Username: aymarino  
> Created at: 2018-12-10 21:50:03 UTC  
> Updated at: 2019-05-10 15:58:32 UTC  
> Closed at: 2019-05-10 15:58:32 UTC  
> Url: https://github.com/boostorg/hana/issues/429  

A [paper](https://wg21.link/p1008r1) voted into the C++20 working draft changes the definition of aggregate such that classes with any user-declared ctors are no longer aggregates. The test test/functional.cpp happens to rely on the C++17 behavior with the type `move_only`, which defines the copy ctor as deleted, and creates instances of the type with empty-brace initialization.  
  
The MSVC team builds hana regularly to detect regressions, and found this failure under our build configuration with the `-std:c++latest` switch. We are using the following backwards-compatible patch going forward, which you are free to upstream it if C++20 compatibility is on the library's radar.  
  
```  
diff --git a/test/functional.cpp b/test/functional.cpp  
index 5e090ae79..beb5381b8 100644  
--- a/test/functional.cpp  
+++ b/test/functional.cpp  
@@ -22,6 +22,7 @@ template <int i = 0>  
 struct undefined { };  
  
 struct move_only {  
+    move_only() = default;  
     move_only(move_only&&) = default;  
     move_only(move_only const&) = delete;  
 };  
```  
  
Thanks,  
Andrew Marino  
Visual C++ Team
