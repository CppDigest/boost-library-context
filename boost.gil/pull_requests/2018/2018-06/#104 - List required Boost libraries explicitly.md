# #104 [appveyor] List required Boost libraries explicitly [Merged]

> Username: mloskot  
> Created at: 2018-06-24 08:11:39 UTC  
> Updated at: 2018-06-24 11:36:14 UTC  
> Merged at: 2018-06-24 11:36:09 UTC  
> Closed at: 2018-06-24 11:36:09 UTC  
> Url: https://github.com/boostorg/gil/pull/104  

(This change has already been reviewed as part of #101)  
  
### Description: what does this pull request do?  
  
Group list of deps for easy identification of direct ones.  
Add --quiet option to git submodule commands on Travis CI.  
  
### References (eg. other issues, pull requests)  
  
#101   
  
### Tasklist  
  
- [x] All CI builds and checks have passed (except [ubsan_integer](https://travis-ci.org/boostorg/gil/builds/396014158?utm_source=github_status&utm_medium=notification) still expected to fail)

---
