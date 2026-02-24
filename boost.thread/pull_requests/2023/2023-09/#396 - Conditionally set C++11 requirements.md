# #396 Conditionally set C++11 requirements [Closed]

> Username: Flamefire  
> Created at: 2023-09-14 07:24:50 UTC  
> Updated at: 2024-12-09 10:29:35 UTC  
> Closed at: 2024-12-09 10:29:35 UTC  
> Url: https://github.com/boostorg/thread/pull/396  

Reduced variant of #395 where the requirements are only set when lexical_cast is directly used, i.e. in the pthread case.  
  
However this [fails/failed on Appveyor](https://ci.appveyor.com/project/viboes/thread/builds/48013319) due to `date_time` using `lexical_cast` and also `src/win32/thread_primitives.cpp` including atomic which uses `static_assert`  
  
So to me #395 looks more correct but I wanted to provide both solutions for you to choose.

---
