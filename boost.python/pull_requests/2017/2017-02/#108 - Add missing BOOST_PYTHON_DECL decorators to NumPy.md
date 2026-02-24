# #108 Add missing BOOST_PYTHON_DECL decorators to NumPy. [Closed]

> Username: stefanseefeld  
> Created at: 2017-02-15 15:44:13 UTC  
> Updated at: 2018-03-07 21:22:53 UTC  
> Closed at: 2017-02-15 17:00:48 UTC  
> Url: https://github.com/boostorg/python/pull/108  



---

## Comment 1

> Username: jhunold  
> Created_at: 2017-02-15 16:06:52 UTC  
> Url: https://github.com/boostorg/python/pull/108#issuecomment-280053588  

This should not work. You need a separate set of macros for each separate dll.   
Steps:  
- make a copy of `config.hpp`for NumPy  
- change the macros  
- use them  instead of `BOOST_PYTHON_DECL`  
  
I'll try to fix this on Friday

---

## Comment 2

> Username: SPKorhonen  
> Created_at: 2017-02-15 16:17:17 UTC  
> Url: https://github.com/boostorg/python/pull/108#issuecomment-280056657  

@jhunold: Please see my pull request #110 on this same issue #98

---

## Comment 3

> Username: SPKorhonen  
> Created_at: 2017-02-15 16:56:02 UTC  
> Url: https://github.com/boostorg/python/pull/108#issuecomment-280068892  

@stefanseefeld and @jhunold This should probably be closed now as pull request #110 was merged.

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2017-02-15 17:00:48 UTC  
> Url: https://github.com/boostorg/python/pull/108#issuecomment-280070442  

Obsoleted by https://github.com/boostorg/python/commit/bd7b8ecba5c211c3361c4a9af82a7a527bed384e

---
