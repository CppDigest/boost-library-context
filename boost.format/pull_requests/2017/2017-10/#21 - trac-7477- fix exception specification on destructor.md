# #21 trac-7477: fix exception specification on destructor [Merged]

> Username: jeking3  
> Created at: 2017-10-12 18:23:51 UTC  
> Updated at: 2017-10-18 19:29:37 UTC  
> Merged at: 2017-10-13 14:05:30 UTC  
> Closed at: 2017-10-13 14:05:30 UTC  
> Url: https://github.com/boostorg/format/pull/21  

I built this in my fork which can do CI builds:  
  
Fork PR: https://github.com/jeking3/format/pull/4/files  
Appveyor: https://ci.appveyor.com/project/jeking3/format/build/1.0.7-develop  
Travis: https://travis-ci.org/jeking3/format/builds/287127025

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-10-12 19:59:20 UTC  
> Url: https://github.com/boostorg/format/pull/21#issuecomment-336248970  

Curious if anybody has an issue with this change or not, it was proposed in trac a while ago.

---

## Comment 2

> Username: mclow  
> Created_at: 2017-10-13 13:43:33 UTC  
> Url: https://github.com/boostorg/format/pull/21#issuecomment-336456232  

Destructors are noexcept by default, so I believe that the code is correct as written.   
MS recommends not using `/Za`. There's several references to that in the [Trac ticket](https://svn.boost.org/trac10/ticket/7477).

---
