# #48 - error C2039: 'begin': is not a member of 'boost' [Closed]

> Username: MoeMod  
> Created at: 2020-03-20 12:57:35 UTC  
> Updated at: 2020-04-24 09:07:39 UTC  
> Closed at: 2020-04-24 09:07:39 UTC  
> Url: https://github.com/boostorg/coroutine/issues/48  

```  
include\boost\coroutine\asymmetric_coroutine.hpp(2364): error C2039: 'begin': is not a member of 'boost'  
```  
with MSVC 19.25.28610.4 enabling /permissive-  
When turning off /permissive-, there will be no more error.

---

## Comment 1

> Username: olk  
> Created at: 2020-04-24 09:07:38 UTC  
> Url: https://github.com/boostorg/coroutine/issues/48#issuecomment-618896647  

should be fixed now
