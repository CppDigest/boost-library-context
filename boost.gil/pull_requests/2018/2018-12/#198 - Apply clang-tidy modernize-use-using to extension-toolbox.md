# #198 Apply clang-tidy modernize-use-using to extension/toolbox [Merged]

> Username: mloskot  
> Created at: 2018-12-17 14:38:39 UTC  
> Updated at: 2018-12-17 18:33:39 UTC  
> Merged at: 2018-12-17 18:25:20 UTC  
> Closed at: 2018-12-17 18:25:20 UTC  
> Url: https://github.com/boostorg/gil/pull/198  

Run clang-tidy 7.0 with `-checks='-*,modernize-use-using' -fix` against  
single TU with `#include <boost/gil/extension/toolbox/*.hpp>`.  
  
Manually refactor numerous `typedef`-s  
- where missed by modernize-use-using check, not uncommon  
- in code snippets in comments  
  
Outcome is that searching for lower-case whole word `typedef`  
in all the `extension/toolbox/*.hpp` should return no matches.  
  
### References  
  
-    #192  
-   #193  
-    #195  
-    #196  
-    #197  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---
