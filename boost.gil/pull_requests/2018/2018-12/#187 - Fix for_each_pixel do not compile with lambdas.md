# #187 Fix for_each_pixel do not compile with lambdas [Closed]

> Username: sdebionne  
> Created at: 2018-12-12 10:30:49 UTC  
> Updated at: 2021-07-16 15:20:37 UTC  
> Closed at: 2018-12-12 12:19:11 UTC  
> Url: https://github.com/boostorg/gil/pull/187  

Fix #186 by replacing the inner for_each loop on lines with a simple range-based loop.  
  
### Environment  
  
Boost 1.68, tested with MSVC 2017.  
  
### References  
  
Fix #186

---

## Comment 1

> Username: sdebionne  
> Created_at: 2018-12-12 12:19:11 UTC  
> Updated_at: 2018-12-12 12:19:22 UTC  
> Url: https://github.com/boostorg/gil/pull/187#issuecomment-446568396  

#139 is a similar fix and is already merged.

---
