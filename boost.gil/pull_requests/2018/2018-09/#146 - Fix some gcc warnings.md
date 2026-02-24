# #146 Fix some gcc warnings [Merged]

> Username: mloskot  
> Created at: 2018-09-28 21:45:21 UTC  
> Updated at: 2018-10-04 18:29:47 UTC  
> Merged at: 2018-10-04 18:29:44 UTC  
> Closed at: 2018-10-04 18:29:44 UTC  
> Url: https://github.com/boostorg/gil/pull/146  

An attempt to clean up several warnings picked randomly:  
  
* Replace unsigned with signed for int as compile-time index.  
* Use GCC pragma to disable `-Wconversion` and `-Wfloat-equal`.  
* Add explicit conversion of arithmetic result to expected result type.   
  
There also have been superfluous trailing whitespaces auto-trimmed (`.editorconfig` in action).  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---
