# #2 bind: silence warning with gcc [Merged]

> Username: timblechmann  
> Created at: 2013-12-07 11:17:58 UTC  
> Updated at: 2014-06-12 07:23:10 UTC  
> Merged at: 2013-12-07 17:14:54 UTC  
> Closed at: 2013-12-07 17:14:54 UTC  
> Url: https://github.com/boostorg/bind/pull/2  

workaround for https://svn.boost.org/trac/boost/ticket/8971

---

## Comment 1

> Username: pdimov  
> Created_at: 2013-12-07 13:07:02 UTC  
> Url: https://github.com/boostorg/bind/pull/2#issuecomment-30054577  

Isn't this likely to produce a diagnostic on all g++ versions that don't yet support the pragma?

---

## Comment 2

> Username: timblechmann  
> Created_at: 2013-12-07 13:17:34 UTC  
> Url: https://github.com/boostorg/bind/pull/2#issuecomment-30054752  

good point! updated the PR

---

## Comment 3

> Username: pdimov  
> Created_at: 2013-12-07 13:33:25 UTC  
> Url: https://github.com/boostorg/bind/pull/2#issuecomment-30055021  

The check as written will disable it for 4.7.0 and enable it for 4.7.1, if I'm not mistaken. Is this what we want? Did the warning appear in 4.7.1? I'd have expected either >= 40700 or >= 40800, but I'm not entirely sure.

---

## Comment 4

> Username: timblechmann  
> Created_at: 2013-12-07 13:40:16 UTC  
> Url: https://github.com/boostorg/bind/pull/2#issuecomment-30055126  

true again ... should have been >=

---
