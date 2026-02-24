# #49 - sprintf deprecated [Open]

> Username: sdarwin  
> Created at: 2025-08-21 14:49:27 UTC  
> Updated at: 2025-08-21 14:49:27 UTC  
> Url: https://github.com/boostorg/icl/issues/49  

"Use snprintf if available as some compilers (clang 14.0) issue deprecation warnings for sprintf"  
  
```  
libs/icl/example/toytime.hpp:        sprintf(repr, "%3s:%02d:%02d", getDayString().c_str(), getHours(), getMinutes());  
```
