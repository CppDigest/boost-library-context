# #85 trac-9882: fix -Wshadow errors in serialization (seen during date_time build) [Closed]

> Username: jeking3  
> Created at: 2017-12-27 19:09:28 UTC  
> Updated at: 2018-10-31 23:24:35 UTC  
> Closed at: 2018-10-31 23:24:28 UTC  
> Url: https://github.com/boostorg/serialization/pull/85  

https://svn.boost.org/trac10/ticket/9882

---

## Comment 1

> Username: robertramey  
> Created_at: 2018-05-01 17:41:25 UTC  
> Url: https://github.com/boostorg/serialization/pull/85#issuecomment-385736129  

I've looked at this and I'm not yet convinced.  It seems that this is dependent on the usage of the date-time library and should be fixed there.

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-05-01 18:25:09 UTC  
> Url: https://github.com/boostorg/serialization/pull/85#issuecomment-385748157  

I will have to look at it again with your findings in mind.  For now let's leave this open?

---
