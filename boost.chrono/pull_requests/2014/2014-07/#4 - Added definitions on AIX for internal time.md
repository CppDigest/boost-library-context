# #4 Added definitions on AIX for internal time. [Closed]

> Username: ismirlian  
> Created at: 2014-07-16 16:06:14 UTC  
> Updated at: 2014-07-16 17:41:02 UTC  
> Closed at: 2014-07-16 17:40:22 UTC  
> Url: https://github.com/boostorg/chrono/pull/4  

A declaration of timegm was not found for AIX resulting in a compilation failure. Both #define  BOOST_CHRONO_INTERNAL_TIMEGM and #define  BOOST_CHRONO_INTERNAL_GMTIME do not take into account the AIX platform even though in the AIX platform it works the same way in all the previously listed platforms.

---

## Comment 1

> Username: viboes  
> Created_at: 2014-07-16 17:15:19 UTC  
> Url: https://github.com/boostorg/chrono/pull/4#issuecomment-49197310  

I can not merge to master directly

---

## Comment 2

> Username: ismirlian  
> Created_at: 2014-07-16 17:40:19 UTC  
> Url: https://github.com/boostorg/chrono/pull/4#issuecomment-49200563  

Ah sorry, I mean to create the pull request for the branch develop. Let me close it and do it again.

---
