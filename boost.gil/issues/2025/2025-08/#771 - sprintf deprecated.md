# #771 - sprintf deprecated [Open]

> Username: sdarwin  
> Created at: 2025-08-21 14:46:41 UTC  
> Updated at: 2025-08-21 14:46:41 UTC  
> Url: https://github.com/boostorg/gil/issues/771  

"Use snprintf if available as some compilers (clang 14.0) issue deprecation warnings for sprintf"  
  
```  
libs/gil/include/boost/gil/extension/io/tiff/detail/log.hpp:        sprintf(buf, fmt, ap);  
libs/gil/include/boost/gil/extension/io/tiff/detail/log.hpp:        sprintf(buf, fmt, ap);  
```
