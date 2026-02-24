# #8 Add missing include to signals2/trackable.hpp [Closed]

> Username: foutrelis  
> Created at: 2014-11-06 07:56:24 UTC  
> Updated at: 2014-11-07 04:35:04 UTC  
> Closed at: 2014-11-07 04:35:04 UTC  
> Url: https://github.com/boostorg/signals2/pull/8  

boost::weak_ptr started being used in commit a0bf2d1 (Disconnect slots  
associated with signals2::trackable immediately) but the matching header  
wasn't included.  
  
https://svn.boost.org/trac/boost/ticket/10100#comment:7

---

## Comment 1

> Username: fmhess  
> Created_at: 2014-11-07 04:35:04 UTC  
> Url: https://github.com/boostorg/signals2/pull/8#issuecomment-62094671  

Thanks, but the merge request should have been for the "develop" branch, not master.  I've pushed your change to develop.

---
