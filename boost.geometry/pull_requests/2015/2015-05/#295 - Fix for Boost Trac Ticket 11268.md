# #295 Fix for Boost Trac Ticket 11268 [Merged]

> Username: mkaravel  
> Created at: 2015-05-11 10:40:22 UTC  
> Updated at: 2015-05-11 21:40:17 UTC  
> Merged at: 2015-05-11 13:44:09 UTC  
> Closed at: 2015-05-11 13:44:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/295  

This PR solves the issue reported in [Boost Trac Ticket 11268](https://svn.boost.org/trac/boost/ticket/11268).  
  
The problem was that the function `detail::intersection::clip_range_with_box()` was passed a different number of arguments that the one the function was expecting.  
  
**Fix:** the function, as well as those calling it, hve been modified to expect (respectively, pass) the correct number of arguments.

---
