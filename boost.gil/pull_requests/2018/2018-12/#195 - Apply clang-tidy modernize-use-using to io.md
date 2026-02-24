# #195 Apply clang-tidy modernize-use-using to io [Merged]

> Username: mloskot  
> Created at: 2018-12-16 02:09:51 UTC  
> Updated at: 2018-12-17 00:07:32 UTC  
> Merged at: 2018-12-16 23:48:59 UTC  
> Closed at: 2018-12-16 23:48:59 UTC  
> Url: https://github.com/boostorg/gil/pull/195  

Run clang-tidy 7.0 with `-checks='-*,modernize-use-using' -fix` against single TU with `#include <boost/gil/io/*.hpp>`.  
  
Manually refactor numerous `typedef`-s  
- where missed by modernize-use-using check, not uncommon  
- in code snippets in comments  
  
Outcome is that searching for lower-case whole word `typedef` in all `io/*.hpp` should return no matches.  
  
### References  
  
- #192  
- #193  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-12-16 23:16:32 UTC  
> Url: https://github.com/boostorg/gil/pull/195#issuecomment-447685822  

@stefanseefeld And this?

---
