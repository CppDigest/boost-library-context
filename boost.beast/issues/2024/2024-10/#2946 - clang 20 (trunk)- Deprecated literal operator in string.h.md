# #2946 - clang 20 (trunk): Deprecated literal operator in string.h [Closed]

> Username: jbulow  
> Created at: 2024-10-30 07:58:16 UTC  
> Updated at: 2024-11-05 13:20:37 UTC  
> Closed at: 2024-11-05 13:20:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2946  

```  
boost/beast/core/detail/string.hpp:26:12: error: identifier '_sv' preceded by whitespace in a literal operator declaration is deprecated [-Werror,-Wdeprecated-literal-operator]  
   26 | operator"" _sv(char const* p, std::size_t n)  
      | ~~~~~~~~~~~^~~  
      | operator""_sv  
1 error generated.  
```
