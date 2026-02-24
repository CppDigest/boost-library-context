# #190 Sync queue time jump fixes [Closed]

> Username: austin-beer  
> Created at: 2017-11-01 16:56:21 UTC  
> Updated at: 2019-03-31 02:45:07 UTC  
> Closed at: 2017-11-08 04:20:10 UTC  
> Url: https://github.com/boostorg/thread/pull/190  

Please review this PR closely when you get the chance. I made several changes/bug fixes while reviewing the code in addition to fixing the time jump issues. See the change log for details.  
  
Here are the before and after time jump test results for this PR:  
https://github.com/austin-beer/test-time-jumps/blob/master/linux_results_fixed1_default.txt  
https://github.com/austin-beer/test-time-jumps/blob/master/linux_results_austin1_default.txt

---

## Review 1 [Commented]

> Username: viboes  
> Created_at: 2017-11-07 17:46:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/190#pullrequestreview-74844430  

📁 include/boost/thread/shared_mutex.hpp

```diff
  15 | #if defined(BOOST_THREAD_PROVIDES_GENERIC_SHARED_MUTEX_ON_WIN)
  16 |+ #if defined(BOOST_THREAD_V2_SHARED_MUTEX)
  17 |+ #include <boost/thread/v2/shared_mutex.hpp>
```

> Username: viboes  
> Created_at: 2017-11-07 17:46:32 UTC  
> Url: https://github.com/boostorg/thread/pull/190#discussion_r149449700  

I don't remember why I've two generic implementation. Wondering what are the differences?

> Username: austin-beer  
> Created_at: 2017-11-07 20:06:49 UTC  
> Url: https://github.com/boostorg/thread/pull/190#discussion_r149488961  

The v2/shared_mutex.hpp implementation was one you added back in 2013 based on an implementation by H. Hinnant. I don't know if it's a better implementation than the existing one or not.


---

## Review 2 [Commented]

> Username: viboes  
> Created_at: 2017-11-07 17:47:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/190#pullrequestreview-74844785  

📁 include/boost/thread/concurrent_queues/detail/sync_deque_base.hpp

```diff
  56 | 
  57 |     inline underlying_queue_type underlying_queue() {
  58 |-       lock_guard<mutex> lk(mtx_);
```

> Username: viboes  
> Created_at: 2017-11-07 17:47:43 UTC  
> Url: https://github.com/boostorg/thread/pull/190#discussion_r149450011  

While you are right that the cost is minimal, I don't see why to pay for if we can avoid it.


---

## Review 3 [Commented]

> Username: viboes  
> Created_at: 2017-11-07 17:56:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/190#pullrequestreview-74847524  

📁 include/boost/thread/concurrent_queues/detail/sync_deque_base.hpp

```diff
  12 | //////////////////////////////////////////////////////////////////////////////
  13 | 
  14 |+ #include <boost/bind.hpp>
```

> Username: viboes  
> Created_at: 2017-11-07 17:56:06 UTC  
> Url: https://github.com/boostorg/thread/pull/190#discussion_r149452291  

Do you mind to create another PR with this change only?

> Username: austin-beer  
> Created_at: 2017-11-07 20:04:09 UTC  
> Url: https://github.com/boostorg/thread/pull/190#discussion_r149488286  

I can do that. Which change specifically are you referring to?

> Username: viboes  
> Created_at: 2017-11-07 23:39:22 UTC  
> Url: https://github.com/boostorg/thread/pull/190#discussion_r149538109  

These ones https://github.com/boostorg/thread/pull/190/commits/5371b04154e73de9abbab1267b26676e9f748059  
  
You can add also the shared_mutex changes https://github.com/boostorg/thread/pull/190/commits/c5ab2d6865e001c408de6b962a27d4f3f0304982, but we need to see what is the added value.


---

## Comment 4

> Username: austin-beer  
> Created_at: 2017-11-08 04:20:10 UTC  
> Url: https://github.com/boostorg/thread/pull/190#issuecomment-342706620  

Replacing this PR with PR #191.

---
