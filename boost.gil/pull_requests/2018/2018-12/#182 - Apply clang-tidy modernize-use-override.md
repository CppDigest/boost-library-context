# #182 Apply clang-tidy modernize-use-override [Merged]

> Username: mloskot  
> Created at: 2018-12-10 12:36:27 UTC  
> Updated at: 2018-12-10 22:10:37 UTC  
> Merged at: 2018-12-10 22:10:23 UTC  
> Closed at: 2018-12-10 22:10:23 UTC  
> Url: https://github.com/boostorg/gil/pull/182  

Used clang-tidy 7.0 with the command:  
  
```  
run-clang-tidy.py \  
    -header-filter='boost\/gil\/.*'  
    -checks='-*,modernize-use-override' -fix  
```  
  
### References  
  
- #180  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-12-10 22:10:36 UTC  
> Url: https://github.com/boostorg/gil/pull/182#issuecomment-445993589  

Thanks for having a look @stefanseefeld

---
