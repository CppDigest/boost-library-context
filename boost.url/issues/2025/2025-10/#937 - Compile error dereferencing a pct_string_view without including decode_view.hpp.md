# #937 - Compile error dereferencing a pct_string_view without including decode_view.hpp [Closed]

> Username: alandefreitas  
> Created at: 2025-10-22 22:00:38 UTC  
> Updated at: 2026-01-22 21:56:21 UTC  
> Closed at: 2026-01-22 21:56:21 UTC  
> Url: https://github.com/boostorg/url/issues/937  

Dereferencing a pct_string_view when decode_view.hpp is not included:  
  
```  
...\router.cpp(91,12): error C2027: use of undefined type 'boost::urls::decode_view'  
      ...\boost\libs\url\include\boost\url\pct_string_view.hpp(31,7):  
```
