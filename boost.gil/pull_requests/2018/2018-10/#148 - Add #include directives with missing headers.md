# #148 Add #include directives with missing headers [Merged]

> Username: mloskot  
> Created at: 2018-10-03 21:30:55 UTC  
> Updated at: 2018-10-04 18:06:49 UTC  
> Merged at: 2018-10-04 18:06:37 UTC  
> Closed at: 2018-10-04 18:06:38 UTC  
> Url: https://github.com/boostorg/gil/pull/148  

Make core and io headers self-contained.  
Required by test to verify headers are self-contained in #147.  
  
-----  
  
The missing headers revealed using the test from #147 using GCC 5.5 locally.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---
