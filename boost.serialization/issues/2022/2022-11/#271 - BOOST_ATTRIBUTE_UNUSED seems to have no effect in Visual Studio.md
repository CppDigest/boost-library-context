# #271 - BOOST_ATTRIBUTE_UNUSED seems to have no effect in Visual Studio [Closed]

> Username: mojca  
> Created at: 2022-11-15 18:04:09 UTC  
> Updated at: 2023-08-26 08:29:09 UTC  
> Closed at: 2023-08-26 08:29:09 UTC  
> Url: https://github.com/boostorg/serialization/issues/271  

I'm compiling Boost 1.80.0 from source as part of a large project build. I'm using GitHub actions on `windows-2022` with Visual Studio 2022, CMake and "treat warnings as errors" turned on.  
  
I'm hitting the following error:  
```  
D:\a\Project\build\__external\src\boost\libs\serialization\include\boost/archive/iterators/wchar_from_mb.hpp(170,31): error C2220: the following warning is treated as an error (compiling source file D:\a\Project\build\__external\src\boost\libs\serialization\src\xml_woarchive.cpp) [D:\a\Project\build\_deps\boost-build\libs\serialization\boost_wserialization.vcxproj]  
D:\a\Project\build\__external\src\boost\libs\serialization\include\boost/archive/iterators/wchar_from_mb.hpp(170,31): warning C4189: 'r': local variable is initialized but not referenced (compiling source file D:\a\Project\build\__external\src\boost\libs\serialization\src\xml_woarchive.cpp) [D:\a\Project\build\_deps\boost-build\libs\serialization\boost_wserialization.vcxproj]  
D:\a\Project\build\__external\src\boost\libs\serialization\include\boost/archive/iterators/wchar_from_mb.hpp(149): message : while compiling class template member function 'void boost::archive::iterators::wchar_from_mb<boost::archive::iterators::xml_escape<InputIterator>>::drain(void)' [D:\a\Project\build\_deps\boost-build\libs\serialization\boost_wserialization.vcxproj]  
```  
I have found:  
* https://github.com/boostorg/serialization/issues/145 and   
* https://github.com/boostorg/serialization/commit/7d79dc7146ac3d0bc76dc5e32ed7aa9411053518  
  
It seems as if VS 2022 doesn't properly understand `BOOST_ATTRIBUTE_UNUSED`.

---

## Comment 1

> Username: mojca  
> Created at: 2022-11-15 22:15:21 UTC  
> Url: https://github.com/boostorg/serialization/issues/271#issuecomment-1315931483  

Would a fix like this be acceptable?  
```diff  
diff --git a/include/boost/config/detail/suffix.hpp b/include/boost/config/detail/suffix.hpp  
index 13d4bb6a..4b25f764 100644  
--- a/include/boost/config/detail/suffix.hpp  
+++ b/include/boost/config/detail/suffix.hpp  
@@ -1039,7 +1039,11 @@ namespace std{ using ::type_info; }  
 // Unused variable/typedef workarounds:  
 //  
 #ifndef BOOST_ATTRIBUTE_UNUSED  
-#  define BOOST_ATTRIBUTE_UNUSED  
+#  if __cplusplus < 201703L  
+#    define BOOST_ATTRIBUTE_UNUSED  
+#  else  
+#    define BOOST_ATTRIBUTE_UNUSED [[maybe_unused]]  
+#  endif  
 #endif  
 //  
 // [[nodiscard]]:  
```

---

## Comment 2

> Username: jzmaddock  
> Created at: 2023-08-25 08:43:52 UTC  
> Url: https://github.com/boostorg/serialization/issues/271#issuecomment-1692999391  

I'll look into this shortly.
