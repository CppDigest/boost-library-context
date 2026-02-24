# #134 Fix warning: comparing floating point with == is unsafe [Merged]

> Username: mloskot  
> Created at: 2018-09-18 18:13:05 UTC  
> Updated at: 2018-09-23 19:07:54 UTC  
> Merged at: 2018-09-23 19:07:24 UTC  
> Closed at: 2018-09-23 19:07:24 UTC  
> Url: https://github.com/boostorg/gil/pull/134  

Trick compiler by using `>=` instead of `==`, where LHS color is guaranteed to never be greater than RHS color value.  
  
### Environment  
  
All relevant information like:  
  
- Compiler version: GCC 8.x  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-09-23 19:07:51 UTC  
> Url: https://github.com/boostorg/gil/pull/134#issuecomment-423839903  

Thanks for review @stefanseefeld

---
