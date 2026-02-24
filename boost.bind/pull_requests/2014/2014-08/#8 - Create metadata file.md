# #8 Create metadata file. [Merged]

> Username: danieljames  
> Created at: 2014-08-18 13:57:39 UTC  
> Updated at: 2014-08-21 20:55:38 UTC  
> Merged at: 2014-08-21 20:55:38 UTC  
> Closed at: 2014-08-21 20:55:38 UTC  
> Url: https://github.com/boostorg/bind/pull/8  

This file is currently used to automatically update the library list on the  
website. It might also be used for other thing in the future. It's not  
required, but it is helpful.

---

## Comment 1

> Username: pdimov  
> Created_at: 2014-08-21 10:37:51 UTC  
> Url: https://github.com/boostorg/bind/pull/8#issuecomment-52903765  

`ref` is now in core, so it should probably not be here?

---

## Comment 2

> Username: danieljames  
> Created_at: 2014-08-21 10:57:59 UTC  
> Url: https://github.com/boostorg/bind/pull/8#issuecomment-52905354  

That's up to you. Move it if it should be moved, just keep the key the same.

---

## Comment 3

> Username: danieljames  
> Created_at: 2014-08-21 11:03:49 UTC  
> Url: https://github.com/boostorg/bind/pull/8#issuecomment-52905822  

Btw. there are two copies of the ref documentation, libs/libraries.htm links to libs/bind/ref.html which redirects to the old one.  
  
http://www.boost.org/doc/libs/1_56_0/libs/core/doc/html/core/ref.html  
http://www.boost.org/doc/libs/1_56_0/doc/html/ref.html  
  
If it's in core, really should be able to link to libs/core/ref.html or libs/core/ref/.

---

## Comment 4

> Username: pdimov  
> Created_at: 2014-08-21 11:05:20 UTC  
> Url: https://github.com/boostorg/bind/pull/8#issuecomment-52905940  

I've no idea if it should be moved or not; but `ref.hpp` is in core, and its documentation in qbk format is in core as well.

---

## Comment 5

> Username: danieljames  
> Created_at: 2014-08-21 11:07:23 UTC  
> Url: https://github.com/boostorg/bind/pull/8#issuecomment-52906093  

Its documentation in boostbook format is still in bind: https://github.com/boostorg/bind/blob/master/doc/ref.xml.

---

## Comment 6

> Username: pdimov  
> Created_at: 2014-08-21 11:10:36 UTC  
> Url: https://github.com/boostorg/bind/pull/8#issuecomment-52906340  

I'll delete it.

---

## Comment 7

> Username: pdimov  
> Created_at: 2014-08-21 11:26:21 UTC  
> Url: https://github.com/boostorg/bind/pull/8#issuecomment-52907592  

Deleted from develop.

---
