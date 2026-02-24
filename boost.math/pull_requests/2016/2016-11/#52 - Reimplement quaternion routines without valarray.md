# #52 Reimplement quaternion routines without valarray [Closed]

> Username: matwey  
> Created at: 2016-11-05 11:42:12 UTC  
> Updated at: 2017-10-06 18:29:10 UTC  
> Closed at: 2017-10-06 18:29:10 UTC  
> Url: https://github.com/boostorg/math/pull/52  

The major issue is that std::vallarray allocates heap memory at construction.  
This is time-consuming operation and leads to unnecessary dramatic performance loss.  
  
See discussion for reference: http://lists.boost.org/boost-users/2015/07/84711.php

---

## Comment 1

> Username: zamazan4ik  
> Created_at: 2017-09-02 18:17:47 UTC  
> Url: https://github.com/boostorg/math/pull/52#issuecomment-326761277  

Hello. Is it still required? If yes, can you review and merge it please?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-09-02 19:08:12 UTC  
> Url: https://github.com/boostorg/math/pull/52#issuecomment-326763802  

Hi Alex, the basic issue is that this class could use a complete re-write to bring it into the 21st century.  I'll try to look into this PR some time soon.

---

## Comment 3

> Username: matwey  
> Created_at: 2017-09-27 11:04:37 UTC  
> Url: https://github.com/boostorg/math/pull/52#issuecomment-332486626  

Why can't it be more faster 20th century version until it will be rewritten into 21st?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2017-10-06 18:29:10 UTC  
> Url: https://github.com/boostorg/math/pull/52#issuecomment-334834733  

See https://github.com/boostorg/math/pull/90

---
