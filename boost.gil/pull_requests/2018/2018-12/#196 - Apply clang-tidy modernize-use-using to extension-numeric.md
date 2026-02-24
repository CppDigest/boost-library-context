# #196 Apply clang-tidy modernize-use-using to extension/numeric [Merged]

> Username: mloskot  
> Created at: 2018-12-16 22:24:56 UTC  
> Updated at: 2018-12-16 23:17:27 UTC  
> Merged at: 2018-12-16 23:17:24 UTC  
> Closed at: 2018-12-16 23:17:24 UTC  
> Url: https://github.com/boostorg/gil/pull/196  

Run clang-tidy 7.0 with `-checks='-*,modernize-use-using' -fix` against  
single TU with `#include <boost/gil/extension/numeric/*.hpp>`.  
  
Manually refactor numerous `typedef`-s  
- where missed by modernize-use-using check, not uncommon  
- in code snippets in comments  
  
Outcome is that searching for lower-case whole word `typedef`  
in all the `extension/numeric/*.hpp` should return no matches,  
  
### References  
  
- #192  
- #193  
- #195  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---
