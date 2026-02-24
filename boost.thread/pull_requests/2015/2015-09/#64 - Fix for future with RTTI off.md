# #64 Fix for future with RTTI off [Merged]

> Username: MarcelRaad  
> Created at: 2015-09-11 09:07:59 UTC  
> Updated at: 2015-12-21 10:34:48 UTC  
> Merged at: 2015-09-11 16:32:59 UTC  
> Closed at: 2015-09-11 16:32:59 UTC  
> Url: https://github.com/boostorg/thread/pull/64  

https://github.com/boostorg/thread/commit/895b4a0ac511f15fdb11964ad4de812695682499 introduced dynamic_casts. This breaks boost::future with RTTI turned off, which worked before. static_casts should be enough here.

---

## Comment 1

> Username: viboes  
> Created_at: 2015-09-11 16:32:53 UTC  
> Url: https://github.com/boostorg/thread/pull/64#issuecomment-139592648  

Yes, we don't need dynamic_cast here. Thanks for the PR.

---
