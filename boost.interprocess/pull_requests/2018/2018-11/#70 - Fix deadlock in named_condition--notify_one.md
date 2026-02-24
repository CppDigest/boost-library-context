# #70 Fix deadlock in named_condition::notify_one [Merged]

> Username: KonstantinKhabarlak  
> Created at: 2018-11-28 11:46:57 UTC  
> Updated at: 2018-12-02 14:29:58 UTC  
> Merged at: 2018-12-02 12:33:53 UTC  
> Closed at: 2018-12-02 12:33:53 UTC  
> Url: https://github.com/boostorg/interprocess/pull/70  

Avoid deadlock in named_condition::notify_one when BOOST_INTERPROCESS_ENABLE_TIMEOUT_WHEN_LOCKING is defined  
  
Fixes issue #69

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2018-12-02 12:34:24 UTC  
> Url: https://github.com/boostorg/interprocess/pull/70#issuecomment-443504216  

Many thanks for the patch

---
