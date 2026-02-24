# #294 - Cannot use with Unreal Engine [Closed]

> Username: egorpugin  
> Created at: 2023-09-22 18:44:12 UTC  
> Updated at: 2023-09-22 19:15:03 UTC  
> Closed at: 2023-09-22 19:15:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/294  

UE has `check` macro that interferres with some filesystem checks.  
  
```  
2>include\boost\filesystem\detail\path_traits.hpp(487): error C2059: syntax error: 'function-style cast'  
2>include\boost\filesystem\detail\path_traits.hpp(487): error C2143: syntax error: missing ';' before '{'  
2>include\boost\filesystem\detail\path_traits.hpp(487): error C2447: '{': missing function header (old-style formal list?)  
```  
  
Wrapping lines 487-514 with the following helps  
```  
#ifdef check  
#pragma push_macro("check")  
#undef check  
#define check_macro_pushed  
#endif  
  
lines 487-514  
  
#ifdef check_macro_pushed  
#undef check_macro_pushed  
#pragma pop_macro("check")  
#endif  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2023-09-22 19:15:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/294#issuecomment-1731924404  

Please report this to UE devs. No project should define unprefixed lower-case macros in its public headers.
