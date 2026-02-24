# #59 - sprintf deprecated [Closed]

> Username: sdarwin  
> Created at: 2025-08-21 14:56:48 UTC  
> Updated at: 2025-12-23 22:02:27 UTC  
> Closed at: 2025-12-23 22:02:27 UTC  
> Url: https://github.com/boostorg/qvm/issues/59  

"Use snprintf if available as some compilers (clang 14.0) issue deprecation warnings for sprintf"  
  
```  
libs/qvm/gen/gen.cpp:                sprintf(buf,INCLUDE_MAT_ASSIGN,d);  
libs/qvm/gen/gen.cpp:                sprintf(buf,INCLUDE_VEC_ASSIGN,d);  
```
