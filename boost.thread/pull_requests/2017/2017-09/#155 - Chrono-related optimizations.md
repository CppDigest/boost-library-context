# #155 Chrono-related optimizations [Merged]

> Username: austin-beer  
> Created at: 2017-09-20 19:19:05 UTC  
> Updated at: 2017-09-21 17:26:07 UTC  
> Merged at: 2017-09-21 05:57:13 UTC  
> Closed at: 2017-09-21 05:57:13 UTC  
> Url: https://github.com/boostorg/thread/pull/155  

* Fixed an issue where the results of chrono arithmetic operations weren't being assigned to a common type that's guaranteed to compile.  
* Eliminated unnecessary conversions to chrono::nanoseconds.  
  
As expected, none of the test results changed with these updates.

---

## Review 1 [Commented]

> Username: viboes  
> Created_at: 2017-09-21 05:52:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/155#pullrequestreview-64183817  

📁 include/boost/thread/pthread/mutex.hpp

```diff
 320 |           d = (std::min)(d, Duration(milliseconds(100)));
 321 |-           while ( ! try_lock_until(thread_detail::internal_clock_t::now() + ceil<nanoseconds>(d)))
 321 |+           while ( ! try_lock_until(thread_detail::internal_clock_t::now() + d))
```

> Username: viboes  
> Created_at: 2017-09-21 05:52:22 UTC  
> Url: https://github.com/boostorg/thread/pull/155#discussion_r140154092  

Why not ceil here?

> Username: austin-beer  
> Created_at: 2017-09-21 15:22:15 UTC  
> Url: https://github.com/boostorg/thread/pull/155#discussion_r140274432  

The result of `thread_detail::internal_clock_t::now() + d` will be whatever type is common to both `internal_clock_t::duration` and `Duration`. Since `try_lock_until()` will accept any duration type, converting to nanoseconds is unnecessary. Does that make sense?

> Username: viboes  
> Created_at: 2017-09-21 17:26:07 UTC  
> Url: https://github.com/boostorg/thread/pull/155#discussion_r140306986  

yes :)


---

## Review 2 [Commented]

> Username: viboes  
> Created_at: 2017-09-21 05:52:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/155#pullrequestreview-64183845  

📁 include/boost/thread/pthread/condition_variable_fwd.hpp

```diff
 222 |           d = (std::min)(d, Duration(milliseconds(100)));
 224 |-           while (cv_status::timeout == wait_until(lock, thread_detail::internal_clock_t::now() + ceil<nanoseconds>(d)))
 223 |+           while (cv_status::timeout == wait_until(lock, thread_detail::internal_clock_t::now() + d))
```

> Username: viboes  
> Created_at: 2017-09-21 05:52:37 UTC  
> Url: https://github.com/boostorg/thread/pull/155#discussion_r140154118  

Why not ceil here?


---

## Review 3 [Commented]

> Username: viboes  
> Created_at: 2017-09-21 05:52:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/155#pullrequestreview-64183879  

📁 include/boost/thread/pthread/condition_variable.hpp

```diff
 302 |           d = (std::min)(d, Duration(milliseconds(100)));
 304 |-           while (cv_status::timeout == wait_until(lock, thread_detail::internal_clock_t::now() + ceil<nanoseconds>(d)))
 303 |+           while (cv_status::timeout == wait_until(lock, thread_detail::internal_clock_t::now() + d))
```

> Username: viboes  
> Created_at: 2017-09-21 05:52:57 UTC  
> Url: https://github.com/boostorg/thread/pull/155#discussion_r140154152  

Why not ceil here?


---
