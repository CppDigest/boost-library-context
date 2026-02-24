# #97 fix a mistake in sync_timed_queue<>::pull_until() [Merged]

> Username: rick68  
> Created at: 2016-07-31 04:13:06 UTC  
> Updated at: 2016-08-07 10:58:22 UTC  
> Merged at: 2016-08-07 10:58:22 UTC  
> Closed at: 2016-08-07 10:58:22 UTC  
> Url: https://github.com/boostorg/thread/pull/97  

The type of `super::not_empty_` in `sync_timed_queue<>` is `boost::condition_variable`, and the return type of `wait_until()` is `boost::cv_status`.

---
