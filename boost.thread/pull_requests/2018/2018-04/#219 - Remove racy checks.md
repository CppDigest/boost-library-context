# #219 Remove racy checks [Merged]

> Username: thughes  
> Created at: 2018-04-26 14:32:15 UTC  
> Updated at: 2018-04-27 15:17:52 UTC  
> Merged at: 2018-04-27 12:43:14 UTC  
> Closed at: 2018-04-27 12:43:15 UTC  
> Url: https://github.com/boostorg/thread/pull/219  

These checks may or may not be true depending on how long it takes `pt1`/`p1` to finish executing. You can verify this by adding a sleep before the check in which case "is_ready()" is always true.  
  
My guess is this may have come from copy/paste from the `when_all` tests. I did not go through all `when_any` tests extremely thoroughly, so there might be other cases as well.

---
