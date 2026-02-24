# #1980 - C4003: not enough arguments for function-like macro invocati on 'max' [Closed]

> Username: m-kurbanov  
> Created at: 2020-06-10 20:48:54 UTC  
> Updated at: 2020-06-12 19:20:31 UTC  
> Closed at: 2020-06-12 19:20:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1980  

After upgrading to 1.73.0 () from 1.71.0  I'm getting the following warning/error when compiling:  
```  
include/boost/beast/core/impl/file_stdio.ipp(273,71): warning C4003: not enough arguments for function-like macro invocati on 'max'  
include/boost/beast/core/impl/file_stdio.ipp(273,71): error C2589: '(': illegal token on right side of '::'  
include/boost/beast/core/impl/file_stdio.ipp(275,7): error C2062: type 'unknown-type' unexpected  
include/boost/beast/core/impl/file_stdio.ipp(273): error C2144: syntax error: 'unknown-type' should be preceded by '('  
include/boost/beast/core/impl/file_stdio.ipp(273,77): error C2059: syntax error: ')'  
include/boost/beast/core/impl/file_stdio.ipp(274,5): error C2143: syntax error: missing ';' before '{'  
```  
https://github.com/boostorg/beast/blob/f25c904d149a5d0a1c54a5f3d8c9045bf27a4fda/include/boost/beast/core/impl/file_stdio.ipp#L273  
  
Maybe you forgot to wrap the call to std::numeric_limits::max() in parenthesis (from #1687):  
`if (offset > static_cast<std::uint64_t>((std::numeric_limits<long>::max)()))`  
  
```diff  
--- a/include/boost/beast/core/impl/file_stdio.ipp  
+++ b/include/boost/beast/core/impl/file_stdio.ipp  
@@ -270,7 +270,7 @@ seek(std::uint64_t offset, error_code& ec)  
         ec = make_error_code(errc::bad_file_descriptor);  
         return;  
     }  
-    if(offset > static_cast<std::uint64_t>(std::numeric_limits<long>::max()))  
+    if(offset > static_cast<std::uint64_t>((std::numeric_limits<long>::max)()))  
     {  
         ec = make_error_code(errc::invalid_seek);  
         return;  
```  
  
Build info:  
```  
Windows 10.0.18363  
Visual Studio 16 2019  
MSVC x64 19.24.28316.0  
```
