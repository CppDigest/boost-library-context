# #573 interpolate_point_spherical: inline var initialization [Merged]

> Username: Kojoley  
> Created at: 2019-03-19 18:48:38 UTC  
> Updated at: 2019-03-20 11:39:50 UTC  
> Merged at: 2019-03-20 03:30:02 UTC  
> Closed at: 2019-03-20 03:30:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/573  

Fixes msvc < 12 failures.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2019-03-19 21:59:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/573#issuecomment-474601221  

Thanks! But this member should be removed at all and constant created in each member function using it. This is how it's done in the whole library. Are you willing to give it a go or would you prefer us to do it?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2019-03-19 22:09:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/573#issuecomment-474604228  

I have updated the PR. If it is not what you meant I am fine with closing the PR.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2019-03-20 03:29:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/573#issuecomment-474670694  

This is it. Thanks!

---
