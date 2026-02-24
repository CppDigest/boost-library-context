# #249 Update executor.hpp [Merged]

> Username: ikrijan  
> Created at: 2022-05-13 14:50:52 UTC  
> Updated at: 2022-05-13 15:49:56 UTC  
> Merged at: 2022-05-13 15:49:56 UTC  
> Closed at: 2022-05-13 15:49:56 UTC  
> Url: https://github.com/boostorg/process/pull/249  

explicit cast to int to silence this: `error: non-constant-expression cannot be narrowed from type 'unsigned long' to 'int' in initializer list [-Wc++11-narrowing]`

---
