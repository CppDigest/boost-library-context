# #360 Fix undefined behavior in `basic_cstring::rfind` [Merged]

> Username: Flamefire  
> Created at: 2022-10-10 11:42:53 UTC  
> Updated at: 2023-02-06 17:30:19 UTC  
> Merged at: 2023-02-06 17:20:22 UTC  
> Closed at: 2023-02-06 17:20:22 UTC  
> Url: https://github.com/boostorg/test/pull/360  

Getting the pointer to 1-before an array is undefined behavior, i.e. `begin() - 1` is not allowed.  
So modify `rfind` to loop until the front and do a final comparison with `begin()/first` afterwards before returning `npos`.  
Use a better-suited for-loop and modify `find` to look as similar as possible so the difference is easy to spot.  
  
Fixes #357

---
