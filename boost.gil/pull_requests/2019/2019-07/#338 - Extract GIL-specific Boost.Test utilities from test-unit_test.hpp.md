# #338 Extract GIL-specific Boost.Test utilities from test/unit_test.hpp [Merged]

> Username: mloskot  
> Created at: 2019-07-18 13:34:37 UTC  
> Updated at: 2019-07-18 15:49:13 UTC  
> Merged at: 2019-07-18 15:49:05 UTC  
> Closed at: 2019-07-18 15:49:05 UTC  
> Url: https://github.com/boostorg/gil/pull/338  

_Refactoring only, no functional changes._  
  
### Description  
  
<!-- What does this pull request do? -->  
  
Add separate header `test/unit_test_utility.hpp` for printers and  
other utilities for better integration of GIL types with Boost.Test.  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-07-18 15:48:53 UTC  
> Url: https://github.com/boostorg/gil/pull/338#issuecomment-512874310  

This PR passed for on private Travis CI: https://travis-ci.org/mloskot/gil/builds/560484810  
  
![image](https://user-images.githubusercontent.com/80741/61472191-3fa3fe00-a984-11e9-9b6d-942ea286145d.png)  
  
I'm merging without waiting 24h+ for boostorg's Travis CI :-)

---
