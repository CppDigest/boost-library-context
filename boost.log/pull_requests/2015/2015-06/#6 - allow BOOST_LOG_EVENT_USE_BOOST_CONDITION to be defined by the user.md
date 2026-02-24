# #6 allow BOOST_LOG_EVENT_USE_BOOST_CONDITION to be defined by the user [Closed]

> Username: mgaunard  
> Created at: 2015-06-15 10:03:06 UTC  
> Updated at: 2015-07-05 19:05:31 UTC  
> Closed at: 2015-07-05 19:05:31 UTC  
> Url: https://github.com/boostorg/log/pull/6  

This is to allow compatibility with code built with GCC versions older than 4.3  
See https://svn.boost.org/trac/boost/ticket/11398

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-07-05 19:05:31 UTC  
> Url: https://github.com/boostorg/log/pull/6#issuecomment-118652735  

The event implementation has been improved in https://github.com/boostorg/log/commit/30e7f036316d20285f8cecea36ca6a8ac0190125. The choice for implementation should be more stable now.

---
