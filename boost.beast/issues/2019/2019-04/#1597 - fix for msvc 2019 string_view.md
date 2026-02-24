# #1597 - fix for msvc 2019  string_view [Closed]

> Username: arturbac  
> Created at: 2019-04-30 23:52:04 UTC  
> Updated at: 2019-04-30 23:56:05 UTC  
> Closed at: 2019-04-30 23:56:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1597  

Fix required for VS 2019 with C++17 and BOOST_BEAST_USE_STD_STRING_VIEW  
  
### Version of Beast  
  
1.70 official release  
  
### Steps necessary to reproduce the problem  
std::string_view::iterator is not a char const * in msvc 2019 with c++17  
  
--- a/include/boost/beast/http/detail/rfc7230.hpp  
+++ b/include/boost/beast/http/detail/rfc7230.hpp  
@@ -56,13 +56,15 @@ BOOST_BEAST_DECL  
 std::int8_t  
 unhex(char c);  
  
+template<typename iterator>  
 BOOST_BEAST_DECL  
 void  
-skip_ows(char const*& it, char const* end);  
+skip_ows(iterator & it, iterator end);  
  
+template<typename iterator>  
 BOOST_BEAST_DECL  
 void  
-skip_token(char const*& it, char const* last);  
+skip_token(iterator & it, iterator last);  
  
 BOOST_BEAST_DECL  
 string_view  
diff --git a/include/boost/beast/http/detail/rfc7230.ipp b/include/boost/beast/http/detail/rfc7230.ipp  
index 612eaf9a..c6fcc476 100644  
--- a/include/boost/beast/http/detail/rfc7230.ipp  
+++ b/include/boost/beast/http/detail/rfc7230.ipp  
@@ -219,8 +219,9 @@ unhex(char c)  
     return tab[static_cast<unsigned char>(c)];  
 }  
  
+template<typename iterator>  
 void  
-skip_ows(char const*& it, char const* end)  
+skip_ows( iterator & it, iterator end)  
 {  
     while(it != end)  
     {  
@@ -229,9 +230,9 @@ skip_ows(char const*& it, char const* end)  
         ++it;  
     }  
 }  
-  
+template<typename iterator>  
 void  
-skip_token(char const*& it, char const* last)  
+skip_token(iterator & it, iterator last)  
 {  
     while(it != last && is_token_char(*it))  
         ++it;

---

## Comment 1

> Username: djarek  
> Created at: 2019-04-30 23:54:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1597#issuecomment-488155808  

I believe this has been fixed on develop.

---

## Comment 2

> Username: arturbac  
> Created at: 2019-04-30 23:56:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1597#issuecomment-488156110  

ok.
