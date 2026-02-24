# #481 Fix error plane_view_t is not a class or namespace name [Merged]

> Username: mloskot  
> Created at: 2020-04-10 20:12:12 UTC  
> Updated at: 2020-04-11 11:23:38 UTC  
> Merged at: 2020-04-11 11:23:33 UTC  
> Closed at: 2020-04-11 11:23:33 UTC  
> Url: https://github.com/boostorg/gil/pull/481  

Issued when compiling toolbox extension using MSVC 14.x with `-permissive-` flag for strict mode.  
  
### References  
  
Fixes #480  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass (not waiting for Travis on boostorg as Travis here https://travis-ci.org/github/mloskot/gil/builds/673572458 completed fine)

---
