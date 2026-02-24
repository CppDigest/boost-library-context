# #194 Fix posix implementation of move constructor/assignment in file_descriptor [Merged]

> Username: Shauren  
> Created at: 2021-01-16 16:05:32 UTC  
> Updated at: 2021-10-14 08:03:03 UTC  
> Merged at: 2021-10-14 06:20:19 UTC  
> Closed at: 2021-10-14 06:20:19 UTC  
> Url: https://github.com/boostorg/process/pull/194  

Currently move constructor and assignment operator in posix file_descriptor class do not remove the resource from moved-from object, leading to double `close` calls  
  
Test demo: https://godbolt.org/z/7P74EW

---
