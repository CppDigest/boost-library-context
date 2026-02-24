# #210 Apply clang-tidy modernize-use-using to all examples [Merged]

> Username: mloskot  
> Created at: 2019-01-12 15:16:31 UTC  
> Updated at: 2019-01-12 16:36:33 UTC  
> Merged at: 2019-01-12 16:36:30 UTC  
> Closed at: 2019-01-12 16:36:30 UTC  
> Url: https://github.com/boostorg/gil/pull/210  

Run clang-tidy 7.0 with `-checks='-*,modernize-use-using' -fix` against TU-s of examples.  
  
Manually refactor numerous typedef-s where missed by `modernize-use-using` check, not uncommon in code snippets in comments.  
  
Outcome is that searching for lower-case whole word `typedef` in sources of all the tests should return only handful of matches in `boost/gil/extension/dynamic_image/apply_operation_base.hpp`  
  
### References  
  
- #192  
- #193  
- #195  
- #196  
- #197  
- #198  
  
### Tasklist  
  
- [x] All CI builds and checks have passed  
  
-----  
  
@stefanseefeld Nothing exciting to review though :)

---
