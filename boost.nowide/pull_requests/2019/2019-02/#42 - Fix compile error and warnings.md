# #42 Fix compile error and warnings [Closed]

> Username: thehans  
> Created at: 2019-02-21 03:06:17 UTC  
> Updated at: 2019-12-13 18:14:55 UTC  
> Closed at: 2019-12-13 18:14:55 UTC  
> Url: https://github.com/boostorg/nowide/pull/42  

```  
/nowide/cenv.hpp:104:41: error: ISO C++ forbids comparison between pointer and integer [-fpermissive]  
         while(*key_end!='=' && key_end!='\0')  
                                         ^~~~  
```  
and -Wimplicit-fallthrough warnings

---

## Comment 1

> Username: Flamefire  
> Created_at: 2019-12-13 18:14:55 UTC  
> Url: https://github.com/boostorg/nowide/pull/42#issuecomment-565548989  

Thanks. The faulty comparison has been fixed already and I got a commit fixing the fallthrough in the pipeline, so closing this

---
