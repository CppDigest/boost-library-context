# #175 Cleanup: silence Xcode 8.2.1 warnings. [Closed]

> Username: KevinHopps  
> Created at: 2017-10-09 14:44:05 UTC  
> Updated at: 2017-10-09 16:49:18 UTC  
> Closed at: 2017-10-09 16:49:18 UTC  
> Url: https://github.com/boostorg/thread/pull/175  



---

## Comment 1

> Username: viboes  
> Created_at: 2017-10-09 16:49:08 UTC  
> Url: https://github.com/boostorg/thread/pull/175#issuecomment-335215350  

I've already fixed this. I changed check by unlock_if_locked.  
  
            void unlock_if_locked() BOOST_NOEXCEPT

---
