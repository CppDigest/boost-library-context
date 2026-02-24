# #103 Remove dependency on boost::lexical_cast [Merged]

> Username: mloskot  
> Created at: 2018-06-23 23:18:00 UTC  
> Updated at: 2018-06-24 21:59:03 UTC  
> Merged at: 2018-06-24 21:59:00 UTC  
> Closed at: 2018-06-24 21:59:00 UTC  
> Url: https://github.com/boostorg/gil/pull/103  

### Description: what does this pull request do?  
  
Replace `lexical_cast` with C++11 `std::to_string` function.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed (except [ubsan_integer](https://travis-ci.org/boostorg/gil/builds/396014158) still expected to fail)

---

## Review 1 [Approved]

> Username: chhenning  
> Created_at: 2018-06-23 23:22:42 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/103#pullrequestreview-131407952  

Perfect!

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-06-23 23:26:30 UTC  
> Url: https://github.com/boostorg/gil/pull/103#issuecomment-399717237  

Ups, need to spell-check the commit log tomorrow - no more late night typing today

---
