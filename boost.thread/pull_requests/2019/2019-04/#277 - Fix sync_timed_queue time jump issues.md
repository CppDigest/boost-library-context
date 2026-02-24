# #277 Fix sync_timed_queue time jump issues [Merged]

> Username: austin-beer  
> Created at: 2019-04-01 16:33:17 UTC  
> Updated at: 2019-04-03 18:43:09 UTC  
> Merged at: 2019-04-03 18:41:52 UTC  
> Closed at: 2019-04-03 18:41:52 UTC  
> Url: https://github.com/boostorg/thread/pull/277  

* Added missing sync_timed_queue::pull_for() time jump tests.  
* Added pull_until() and pull_for() to the test for #271.  
* Fixed time jump issues that were re-introduced while fixing #271.  
* Fixed time jump issues with sync_timed_queue::pull_for().

---

## Review 1 [Commented]

> Username: viboes  
> Created_at: 2019-04-03 04:48:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/277#pullrequestreview-222003669  

📁 include/boost/thread/concurrent_queues/sync_timed_queue.hpp

```diff
  92 |-     queue_op_status pull_until(chrono::time_point<WClock,Duration> const& tp, T& elem);
  91 |+     template <class Duration>
  92 |+     queue_op_status pull_until(chrono::time_point<clock,Duration> const& tp, T& elem);
```

> Username: viboes  
> Created_at: 2019-04-03 04:48:20 UTC  
> Updated_at: 2019-04-03 04:48:21 UTC  
> Url: https://github.com/boostorg/thread/pull/277#discussion_r271577614  

I'm not saying the implementation was correct, but why you don't want to support other clocks?

> Username: austin-beer  
> Created_at: 2019-04-03 14:28:35 UTC  
> Updated_at: 2019-04-03 14:36:35 UTC  
> Url: https://github.com/boostorg/thread/pull/277#discussion_r271770715  

Currently, if you create a `sync_timed_queue<chrono::steady_clock>` and then call `pull_until()` and pass it a `chrono::system_clock` (or any other clock) time point, the code will fail to compile because it tries to compare `tp` against `clock::now()` in `wait_to_pull_until()`. So we have two options:  
  
1. Add code to convert between clocks in `wait_to_pull_until()`, which adds complexity to the code.  
2. Change `pull_until()` to make it clear that mixing clocks is not supported.  
  
I chose number 2 because that's the existing behavior and because it keeps the implementation simpler and because I can't think of a good use-case where someone would need to mix clocks. What do you think?

> Username: viboes  
> Created_at: 2019-04-03 18:40:33 UTC  
> Url: https://github.com/boostorg/thread/pull/277#discussion_r271879437  

Lets go with approach 2 by now. I will see if I can find out a valid use case.


---

## Review 2 [Commented]

> Username: viboes  
> Created_at: 2019-04-03 04:50:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/277#pullrequestreview-222003965  

📁 include/boost/thread/concurrent_queues/sync_timed_queue.hpp

```diff
 125 |-     template <class WClock, class Duration>
 126 |-     queue_op_status wait_to_pull_until(unique_lock<mutex>&, chrono::time_point<WClock, Duration> const& tp);
 125 |+     queue_op_status wait_to_pull_until(unique_lock<mutex>&, TimePoint const& tp);
```

> Username: viboes  
> Created_at: 2019-04-03 04:50:12 UTC  
> Url: https://github.com/boostorg/thread/pull/277#discussion_r271577869  

Why do you want to be more generic here? Do you have other timepoints other than boost::chrono::timepoint? Are you thinking on std::chrono::timepoint?

> Username: austin-beer  
> Created_at: 2019-04-03 14:32:57 UTC  
> Url: https://github.com/boostorg/thread/pull/277#discussion_r271773103  

Since `wait_to_pull_until()` is an internal/private function, it simplified the code to have the type of `tp` match the type of `super::data_.top().time`, which is also of type `TimePoint`. Then in `pull_until()` I use `time_point_cast()` to convert from the user's time point type to `TimePoint`.

> Username: viboes  
> Created_at: 2019-04-03 18:41:12 UTC  
> Updated_at: 2019-04-03 18:41:13 UTC  
> Url: https://github.com/boostorg/thread/pull/277#discussion_r271879688  

Okay, I see.


---
