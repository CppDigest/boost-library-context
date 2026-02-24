# #510 - sprintf deprecated [Open]

> Username: sdarwin  
> Created at: 2025-08-21 14:58:01 UTC  
> Updated at: 2025-08-21 14:58:01 UTC  
> Url: https://github.com/boostorg/process/issues/510  

"Use snprintf if available as some compilers (clang 14.0) issue deprecation warnings for sprintf"  
  
```  
libs/process/src/pid.cpp:    sprintf(buffer, "/proc/%d/stat", pid);  
```
