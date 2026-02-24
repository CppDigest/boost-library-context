# #11 Disable auto_ptr deprecation warnings on g++/clang [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2017-12-09 16:15:47 UTC  
> Updated at: 2017-12-10 00:28:28 UTC  
> Merged at: 2017-12-09 17:47:48 UTC  
> Closed at: 2017-12-09 17:47:48 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/11  

Hi,  
  
By default, when you use GCC 6, there are some annoying warnings each time auto_ptr are exposed in the API (even when you don't use them). This silences this warnings. It was largely inspired from this commit https://github.com/boostorg/smart_ptr/commit/df904965833dcf1bf2eb4d7d1416369dd806e092 from @pdimov (boost/ptr_container/detail/ptr_container_disable_deprecated.hpp was almost copied as is, I even left the copyright, I hope it is OK).  
  
Cheers,  
Romain

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created_at: 2017-12-09 16:37:45 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/11#issuecomment-350487012  

The build failures are due to issues fixed in #12. Just merge it first, then merge this one to have a clean build.

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2017-12-09 17:09:56 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/11#issuecomment-350490759  

The build is now green. I don't know if you want me to rebase on top of "develop" now that @pdimov  merged #12, just let me know.

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-12-09 17:14:28 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/11#issuecomment-350491026  

I don't think that rebasing is necessary.

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-12-09 17:48:23 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/11#issuecomment-350493475  

I think that there are a few warnings still left though.

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-12-09 17:52:44 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/11#issuecomment-350493808  

```  
..\../boost/ptr_container/ptr_unordered_map.hpp:199:9: note: in expansion of macro 'BOOST_PTR_CONTAINER_DEFINE_RELEASE_AND_CLONE'  
```  
and  
```  
..\../boost/ptr_container/ptr_unordered_set.hpp:108:9: note: in expansion of macro 'BOOST_PTR_CONTAINER_DEFINE_RELEASE_AND_CLONE'  
```

---

## Comment 6

> Username: Romain-Geissler-1A  
> Created_at: 2017-12-10 00:28:28 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/11#issuecomment-350515980  

Indeed. Remaining issues are fixed in #13.

---
