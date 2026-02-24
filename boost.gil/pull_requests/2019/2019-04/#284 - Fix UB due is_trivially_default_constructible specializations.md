# #284 Fix UB due is_trivially_default_constructible specializations [Merged]

> Username: mloskot  
> Created at: 2019-04-15 19:11:56 UTC  
> Updated at: 2019-04-15 21:00:21 UTC  
> Merged at: 2019-04-15 21:00:17 UTC  
> Closed at: 2019-04-15 21:00:17 UTC  
> Url: https://github.com/boostorg/gil/pull/284  

>   C++11 / 20.11.2 Header <type_traits> synopsis  
>   1 The behavior of a program that adds specializations for any  
>   of the class templates defined in this subclause is undefined  
>   unless otherwise specified.  
  
The specializations are not used anywhere, so they are safe to remove  
without providing a replacement.  
  
### References  
  
Fixes #283  
  
### Tasklist  
  
- [x] Review  
- [x] All CI builds and checks have passed

---

## Review 1 [Approved]

> Username: stefanseefeld  
> Created_at: 2019-04-15 19:17:45 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/284#pullrequestreview-226832413  

without hesitation ! :-)

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-04-15 19:18:54 UTC  
> Url: https://github.com/boostorg/gil/pull/284#issuecomment-483382587  

All built for me locally with GCC 7.3, so I'm hopeful :)

---

## Comment 3

> Username: mloskot  
> Created_at: 2019-04-15 20:04:54 UTC  
> Url: https://github.com/boostorg/gil/pull/284#issuecomment-483398093  

My private Travis for GIL is all green!   
https://travis-ci.org/mloskot/gil/builds/520437453

---
