# #197 Apply clang-tidy modernize-use-using to extension/io [Merged]

> Username: mloskot  
> Created at: 2018-12-16 23:05:25 UTC  
> Updated at: 2018-12-17 00:24:45 UTC  
> Merged at: 2018-12-17 00:24:31 UTC  
> Closed at: 2018-12-17 00:24:31 UTC  
> Url: https://github.com/boostorg/gil/pull/197  

Run clang-tidy 7.0 with `-checks='-*,modernize-use-using' -fix` against  
single TU with `#include <boost/gil/extension/io/*.hpp>`.  
  
Manually refactor numerous `typedef`-s  
- where missed by modernize-use-using check, not uncommon  
- in code snippets in comments  
  
Outcome is that searching for lower-case whole word `typedef`  
in all the `extension/io/*.hpp` should return no matches.  
  
### References  
  
- #192  
- #193  
- #195  
- #196  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---
