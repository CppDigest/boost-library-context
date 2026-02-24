# #748 - vtable for urls::url not exported on mingw [Closed]

> Username: mitchellwong  
> Created at: 2023-05-04 21:25:17 UTC  
> Updated at: 2023-06-10 00:13:01 UTC  
> Closed at: 2023-06-10 00:13:01 UTC  
> Url: https://github.com/boostorg/url/issues/748  

When building under mingw, the vtable for `urls::url` isn't exported. Comparing the output of ` objdump -p libboost_url-gcc12-mt-x32-1_83.dll | grep _Z | c++filt | grep vtable` for the unmodified source vs one patched with   
```  
diff --git a/include/boost/url/url.hpp b/include/boost/url/url.hpp  
index 9af3592..6e99987 100644  
--- a/include/boost/url/url.hpp  
+++ b/include/boost/url/url.hpp  
@@ -60,7 +60,7 @@ namespace urls {  
         @ref parse_uri_reference,  
         @ref resolve.  
 */  
-class BOOST_SYMBOL_VISIBLE url  
+class BOOST_SYMBOL_VISIBLE BOOST_URL_DECL url  
     : public url_base  
 {  
     friend std::hash<url>;  
```  
```  
	4eb0c	 6354  vtable for __cxxabiv1::__class_type_info  
	4eb34	 6359  vtable for __cxxabiv1::__si_class_type_info  
	4eb60	 6360  vtable for __cxxabiv1::__vmi_class_type_info  
	4eb8c	 6444  vtable for std::out_of_range  
```  
```  
	4eb0c	 6354  vtable for __cxxabiv1::__class_type_info  
	4eb34	 6359  vtable for __cxxabiv1::__si_class_type_info  
	4eb60	 6360  vtable for __cxxabiv1::__vmi_class_type_info  
	4eb8c	 6444  vtable for std::out_of_range  
	[ 390] vtable for boost::urls::url  
```  
It appears while MSVC will export the vtable if you export all the functions of a class, gcc will only export it if you export the class itself.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-05-04 22:53:57 UTC  
> Url: https://github.com/boostorg/url/issues/748#issuecomment-1535504173  

Are you proposing a fix? mingw gives me a headache

---

## Comment 2

> Username: mitchellwong  
> Created at: 2023-05-05 14:20:59 UTC  
> Url: https://github.com/boostorg/url/issues/748#issuecomment-1536336873  

It should be sufficient to add `BOOST_URL_DECL` to the definition of every class that's supposed to be exported from the dll.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-05-05 18:39:06 UTC  
> Url: https://github.com/boostorg/url/issues/748#issuecomment-1536632704  

`BOOST_URL_DECL` or `BOOST_SYMBOL_VISIBLE`? Or both?

---

## Comment 4

> Username: mitchellwong  
> Created at: 2023-05-05 18:49:45 UTC  
> Url: https://github.com/boostorg/url/issues/748#issuecomment-1536642724  

Visibility does nothing on Windows, but you should keep it for other platforms. Presumably there isn't a case where you would want to export on Windows but not have visibility on Linux or vice versa so you might want to just combine the macros.
