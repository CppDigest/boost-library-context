# #7 #else/#endif comment is updated to the condition [Closed]

> Username: mingulov  
> Created at: 2014-03-21 08:43:45 UTC  
> Updated at: 2014-03-24 06:47:22 UTC  
> Closed at: 2014-03-24 06:47:15 UTC  
> Url: https://github.com/boostorg/date_time/pull/7  

# if BOOST_DATE_TIME_HAS_REENTRANT_STD_FUNCTIONS is used but #else and #endif had been commented with 'BOOST_THREADS', which prevents a proper understanding of the source. Fixed by using a proper comment.

---

## Comment 1

> Username: mclow  
> Created_at: 2014-03-21 14:01:44 UTC  
> Url: https://github.com/boostorg/date_time/pull/7#issuecomment-38277166  

This change is fine, but this is the wrong place to merge it.  
It should get committed to the "develop" branch.

---

## Comment 2

> Username: mclow  
> Created_at: 2014-03-21 14:23:23 UTC  
> Url: https://github.com/boostorg/date_time/pull/7#issuecomment-38279505  

Also, please post the pull request to boost-maint@lists.boost.org as well

---

## Comment 3

> Username: mingulov  
> Created_at: 2014-03-24 06:47:15 UTC  
> Url: https://github.com/boostorg/date_time/pull/7#issuecomment-38415996  

Ok, thank you for the fast reply, seems it is impossible to change this pull request to another branch inside this pull request, so a separate pull request will be created for 'develop' branch.

---
