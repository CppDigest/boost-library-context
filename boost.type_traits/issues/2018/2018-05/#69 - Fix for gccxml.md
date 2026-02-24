# #69 - Fix for gccxml [Closed]

> Username: muffgaga  
> Created at: 2018-05-02 11:45:33 UTC  
> Updated at: 2018-05-07 17:50:36 UTC  
> Closed at: 2018-05-07 17:50:36 UTC  
> Url: https://github.com/boostorg/type_traits/issues/69  

We use `gccxml` (some deprecated gcc to XML tool relying on old gcc, e.g. 4.9.3, versions) for code generation and encountered a problem when including `boost/type_traits.hpp` from newer boost versions. The expression for getting the `result_type` size of the common type of `T()` and `U()` somehow generated illegal "node identifiers" for gccxml.  
  
I'm not sure if this is really worth fixing, but it worked for older boost versions and the following workaround fixes the issue for us:  
  
```diff  
diff -rpu boost_1_66_0_old/boost/type_traits/detail/common_arithmetic_type.hpp boost_1_66_0/boost/type_traits/detail/common_arithmetic_type.hpp  
--- boost_1_66_0_old/boost/type_traits/detail/common_arithmetic_type.hpp	2017-12-14 00:56:49.000000000 +0100  
+++ boost_1_66_0/boost/type_traits/detail/common_arithmetic_type.hpp	2018-04-26 14:14:44.020680512 +0200  
@@ -205,10 +205,11 @@ private:  
 #endif  
   
     static bool cond();  
+    static const int selector = sizeof(select( cond()? T(): U() ));  
   
 public:  
   
-    typedef typename arithmetic_type< sizeof(select( cond()? T(): U() )) >::type type;  
+    typedef typename arithmetic_type< selector >::type type;  
 };  
   
 } // namespace type_traits_detail  
```
