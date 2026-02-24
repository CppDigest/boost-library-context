# #199 Apply clang-tidy modernize-use-using to all tests [Merged]

> Username: mloskot  
> Created at: 2018-12-17 18:34:35 UTC  
> Updated at: 2018-12-20 15:15:46 UTC  
> Merged at: 2018-12-17 23:13:40 UTC  
> Closed at: 2018-12-17 23:13:40 UTC  
> Url: https://github.com/boostorg/gil/pull/199  

Run clang-tidy 7.0 with `-checks='-*,modernize-use-using' -fix` against TU-s of all tests.  
  
Manually refactor numerous `typedef`-s  
- where missed by modernize-use-using check, not uncommon  
- in code snippets in comments  
  
Outcome is that searching for lower-case whole word `typedef`  
in sources of all the tests should return no matches.  
  
### References  
  
-    #192  
-   #193  
-    #195  
-    #196  
-    #197  
-    #198  
  
### Tasklist  
  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-12-17 20:57:25 UTC  
> Url: https://github.com/boostorg/gil/pull/199#issuecomment-447996027  

@stefanseefeld  FYI, for this one, I'll just wait for the CI-s and merge.

---
