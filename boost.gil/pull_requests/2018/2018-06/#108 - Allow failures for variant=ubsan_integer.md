# #108 [travis] Allow failures for variant=ubsan_integer [Merged]

> Username: mloskot  
> Created at: 2018-06-25 19:53:54 UTC  
> Updated at: 2018-06-25 21:02:39 UTC  
> Merged at: 2018-06-25 21:02:31 UTC  
> Closed at: 2018-06-25 21:02:31 UTC  
> Url: https://github.com/boostorg/gil/pull/108  

### Description: what does this pull request do?  
  
Currently, UBSan integer checks report several false positives due to unsigned integer overflows (well-defined):  
  
Instead of hiding them completely, keep them displayed on the matrix until we decide how to handle.  
For example, [build 375.9](https://travis-ci.org/boostorg/gil/jobs/396282494#L1181).  
  
### Tasklist  
  
- [x] All CI builds and checks have passed  
  
### References  
  
This has been discussed and agreed with @stefanseefeld, this should help us to keep the `master`-branch to be consistent with the one for `develop`. See #109 for further discussion.

---
