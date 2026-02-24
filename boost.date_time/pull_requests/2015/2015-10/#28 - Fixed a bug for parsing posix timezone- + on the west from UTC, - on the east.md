# #28 Fixed a bug for parsing posix timezone: + on the west from UTC, - on the east [Closed]

> Username: zerkms  
> Created at: 2015-10-06 11:19:11 UTC  
> Updated at: 2015-10-06 20:33:44 UTC  
> Closed at: 2015-10-06 20:33:44 UTC  
> Url: https://github.com/boostorg/date_time/pull/28  

Currently mistakenly the signs are used the other way around  
  
Fixes:  
- https://svn.boost.org/trac/boost/ticket/4545  
- https://svn.boost.org/trac/boost/ticket/3336

---

## Comment 1

> Username: mclow  
> Created_at: 2015-10-06 18:43:26 UTC  
> Url: https://github.com/boostorg/date_time/pull/28#issuecomment-145960177  

Thanks for the PR, but we don't accept pull requests into `master`.  We make changes into `develop`, test there, and then merge to `master`.  I'll be happy to review a PR into `develop`.

---
