# #180 Apply clang-tidy modernize-use-nullptr [Merged]

> Username: mloskot  
> Created at: 2018-12-10 00:24:41 UTC  
> Updated at: 2018-12-10 08:58:41 UTC  
> Merged at: 2018-12-10 08:58:37 UTC  
> Closed at: 2018-12-10 08:58:37 UTC  
> Url: https://github.com/boostorg/gil/pull/180  

(_IMPORTANT: This is a pilot PR with automatic code refactoring by clang-tidy. There are more PRs planned with basic/fundamental C++11 modernization, with every check added to the `.clang-tidy` configuration file as a record tracking checks already applied._)  
  
Used clang-tidy 7.0 with the command:  
  
```  
  run-clang-tidy.py \  
      -header-filter='boost\/gil\/.*' \  
      -checks='-*,modernize-use-nullptr' -fix  
```  
  
Update `CONTRIBUTING.md` on how to generate compile command database and run clang-tidy.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---
