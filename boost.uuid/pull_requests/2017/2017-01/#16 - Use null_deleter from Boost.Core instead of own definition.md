# #16 Use null_deleter from Boost.Core instead of own definition [Merged]

> Username: tklauser  
> Created at: 2017-01-11 09:10:37 UTC  
> Updated at: 2017-10-18 19:34:41 UTC  
> Merged at: 2017-01-11 16:54:26 UTC  
> Closed at: 2017-01-11 16:54:26 UTC  
> Url: https://github.com/boostorg/uuid/pull/16  



---

## Comment 1

> Username: mclow  
> Created_at: 2017-01-11 16:25:02 UTC  
> Updated_at: 2017-01-11 16:25:33 UTC  
> Url: https://github.com/boostorg/uuid/pull/16#issuecomment-271915585  

I think that this is a tossup.  
On one hand, it removes a (small) class from Boost.UUID.  
On the other hand, it adds a dependency on Boost.Core.  
On the gripping hand, most everything in boost already depends on Boost.Core.  
  
My feeling is that this should be accepted.

---

## Comment 2

> Username: tklauser  
> Created_at: 2017-01-11 16:49:47 UTC  
> Url: https://github.com/boostorg/uuid/pull/16#issuecomment-271923161  

boost/uuid/seed_rng.hpp already has a dependency on a header from Boost.Core (boost/core/noncopyable.hpp), so I thought it should be fine add another dependency. I probably should have mentioned this in the PR, sorry.

---

## Comment 3

> Username: mclow  
> Created_at: 2017-01-11 16:54:48 UTC  
> Url: https://github.com/boostorg/uuid/pull/16#issuecomment-271924586  

Please keep an eye on the test bots, and ping me in a week or so to merge to master.

---

## Comment 4

> Username: tklauser  
> Created_at: 2017-01-11 17:31:27 UTC  
> Url: https://github.com/boostorg/uuid/pull/16#issuecomment-271934982  

Will do. Thanks!

---

## Comment 5

> Username: tklauser  
> Created_at: 2017-01-19 07:50:10 UTC  
> Url: https://github.com/boostorg/uuid/pull/16#issuecomment-273704354  

@mclow pinging for merge to master. The test bots didn't show any failure which I could trace to this change.

---

## Comment 6

> Username: tklauser  
> Created_at: 2017-04-06 07:07:11 UTC  
> Url: https://github.com/boostorg/uuid/pull/16#issuecomment-292087876  

@mclow pinging again for merge to master

---

## Comment 7

> Username: mclow  
> Created_at: 2017-05-04 05:23:48 UTC  
> Url: https://github.com/boostorg/uuid/pull/16#issuecomment-299099248  

This has been done for the 1.64.0 release.

---
