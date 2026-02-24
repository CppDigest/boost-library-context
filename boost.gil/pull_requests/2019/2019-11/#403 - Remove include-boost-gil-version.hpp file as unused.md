# #403 Remove include/boost/gil/version.hpp file as unused [Merged]

> Username: mloskot  
> Created at: 2019-11-06 21:29:31 UTC  
> Updated at: 2019-11-23 00:15:19 UTC  
> Merged at: 2019-11-09 22:09:10 UTC  
> Closed at: 2019-11-09 22:09:10 UTC  
> Url: https://github.com/boostorg/gil/pull/403  

### Description  
  
Removing `version.hpp` added to GIL in https://github.com/boostorg/gil/commit/dfc86f6895bb09c09d9516685667da66053ba8bf (Boost 1.68)  
  
Motivation:  
  
  - GIL is part of Boost collection, so Boost version is sufficient  
  - GIL version makes little sense unless GIL becomes independent  
  - Two distinct version numbers, Boost's and GIL's, are confusing.  
  - Role of GIL's version number is not documented thus not entirely clear  
  
### References  
  
The removal was discussed and agreed upon in this thread  
https://lists.boost.org/boost-gil/2019/11/0346.php  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass  
  
/cc @stefanseefeld, @lpranam

---

## Review 1 [Approved]

> Username: stefanseefeld  
> Created_at: 2019-11-06 21:41:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/403#pullrequestreview-312762848  

Thanks ! (I take it we are not referring to this version anywhere in our docs ?)

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-11-06 21:43:23 UTC  
> Url: https://github.com/boostorg/gil/pull/403#issuecomment-550513816  

Yes, I think I checked all places, and final grep checks return nothing  
  
```  
$ find . -type f | grep -i BOOST_GIL_VERSION  
$ find . -type f | grep -i BOOST_GIL_LIB_VERSION  
```

---
