# #264 - Please don't unconditionally define  _CRT_SECURE_NO_WARNINGS [Open]

> Username: jonesmz  
> Created at: 2025-12-06 16:26:59 UTC  
> Updated at: 2025-12-06 16:39:39 UTC  
> Url: https://github.com/boostorg/regex/issues/264  

Here:  
  
https://github.com/boostorg/regex/blob/ed6ebbd5a52b566e454e0cc34494dd2912c6c66c/src/wide_posix_api.cpp#L19  
  
This introduces a warning in any project that defines this macro globally.  
  
Better is to do  
  
```  
#ifndef  _CRT_SECURE_NO_WARNINGS  
#define  _CRT_SECURE_NO_WARNINGS  
#endif   
```  
  
so that if it's already defined, it won't be re-defined.  
  
This impacted me when i tried out the boost 1.90 beta.
