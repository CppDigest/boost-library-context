# #102 Remove dependency on boost::array [Merged]

> Username: mloskot  
> Created at: 2018-06-23 23:11:45 UTC  
> Updated at: 2018-06-24 21:58:19 UTC  
> Merged at: 2018-06-24 21:57:57 UTC  
> Closed at: 2018-06-24 21:57:57 UTC  
> Url: https://github.com/boostorg/gil/pull/102  

### Description: what does this pull request do?  
  
Replace `boost::array` with C++11 `std::array`.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed (except [ubsan_integer](https://travis-ci.org/boostorg/gil/builds/396014158) still expected to fail)

---

## Review 1 [Approved]

> Username: chhenning  
> Created_at: 2018-06-23 23:21:44 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/102#pullrequestreview-131407940  

Looks good! If that's all we have to do.

---
