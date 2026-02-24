# #923 - Explore "plus" scheme convention [Closed]

> Username: alandefreitas  
> Created at: 2025-08-27 21:45:23 UTC  
> Updated at: 2026-01-29 03:55:56 UTC  
> Closed at: 2026-01-29 03:55:56 UTC  
> Url: https://github.com/boostorg/url/issues/923  

Add a note to docs about the "plus" scheme convention.  
  
```  
std::string_view   
scheme_ex(std::string_view s) {  
    if (auto pos = s.rfind('+'); pos != std::string_view::npos)  
        return s.substr(pos + 1);  
    return {};  
}  
```  
  
https://github.com/whatwg/url/issues/230
