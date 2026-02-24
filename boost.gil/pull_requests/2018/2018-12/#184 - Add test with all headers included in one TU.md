# #184 [cmake] Add test with all headers included in one TU [Merged]

> Username: mloskot  
> Created at: 2018-12-11 19:56:06 UTC  
> Updated at: 2019-03-29 20:29:13 UTC  
> Merged at: 2018-12-11 22:17:28 UTC  
> Closed at: 2018-12-11 22:17:28 UTC  
> Url: https://github.com/boostorg/gil/pull/184  

Rename target `test_compile_headers` to `test_headers_self_contained`.  
Add target `test_headers_all_in_one`  
- Currently tests headers of core, `concepts` and `io`.  
  
### Tasklist  
  
- [x] All CI builds and checks have passed  
  
-----  
  
Note, none of the headers tests are configured to build on the CI builds or the regression builds.  
When CMake configures build locally, all those tests are enabled.

---
