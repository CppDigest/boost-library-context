# #5 Added definitions on AIX for internal time. [Merged]

> Username: ismirlian  
> Created at: 2014-07-16 17:40:36 UTC  
> Updated at: 2014-07-17 21:19:22 UTC  
> Merged at: 2014-07-17 21:19:22 UTC  
> Closed at: 2014-07-17 21:19:22 UTC  
> Url: https://github.com/boostorg/chrono/pull/5  

A declaration of timegm was not found for AIX resulting in a compilation failure. Both #define BOOST_CHRONO_INTERNAL_TIMEGM and #define BOOST_CHRONO_INTERNAL_GMTIME do not take into account the AIX platform even though in the AIX platform it works the same way in all the previously listed platforms.

---
