# #238 Fix compiler warnings about deprecated language features [Merged]

> Username: karzhenkov  
> Created at: 2017-09-18 17:46:59 UTC  
> Updated at: 2021-10-02 22:08:28 UTC  
> Merged at: 2017-10-29 03:50:55 UTC  
> Closed at: 2017-10-29 03:50:55 UTC  
> Url: https://github.com/boostorg/build/pull/238  

Bootstrap log contains warnings about using of old-style C features:  
  
- implicit function declaration;  
- implicit `int.`  
  
By adding some includes, declarations of existing functions, and by explicitly specifying the `int` type we can get clean bootstrap log.   
  
There is also an unnecessary include directive in [src/engine/modules/path.c](https://github.com/boostorg/build/compare/develop...karzhenkov:fix-compiler-warnings?expand=1#diff-d97793838c95293d5f2ece81a49d63de)

---
