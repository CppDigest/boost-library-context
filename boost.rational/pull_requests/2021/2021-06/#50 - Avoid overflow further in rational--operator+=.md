# #50 Avoid overflow further in rational::operator+= [Open]

> Username: vedgy  
> Created at: 2021-06-19 12:32:43 UTC  
> Updated at: 2021-06-19 12:32:43 UTC  
> Url: https://github.com/boostorg/rational/pull/50  

The added test case fails before and passes after the operator+= implementation change when `sizeof(unsigned) == 4` and probably when `sizeof(unsigned) == 8`.  
  
I don't know whether overflowing less justifies the slower implementation. Depends on how much overflow safety is prioritized over performance.

---
