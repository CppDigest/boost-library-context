# #193 Apply clang-tidy modernize-use-using to extension/dynamic_image [Merged]

> Username: mloskot  
> Created at: 2018-12-15 23:36:00 UTC  
> Updated at: 2018-12-16 02:05:50 UTC  
> Merged at: 2018-12-16 00:25:13 UTC  
> Closed at: 2018-12-16 00:25:13 UTC  
> Url: https://github.com/boostorg/gil/pull/193  

Run clang-tidy 7.0 with `-checks='-*,modernize-use-using' -fix`  
against single TU with `#include <boost/gil/extension/dynamic_image/*.hpp>`.  
  
Manually refactor numerous `typedef`-s  
- where missed by `modernize-use-using` check, not uncommon  
- in code snippets in comments  
  
Outcome is that searching for lower-case whole word `typedef`  
in all the `extension/dynamic_image/*.hpp` should return 5 matches,  
in definitions of macros (TODO).  
  
### References  
  
- #192  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---
