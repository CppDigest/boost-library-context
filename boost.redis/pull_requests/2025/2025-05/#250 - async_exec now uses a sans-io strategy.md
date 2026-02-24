# #250 async_exec now uses a sans-io strategy [Merged]

> Username: anarthal  
> Created at: 2025-05-12 20:14:14 UTC  
> Updated at: 2025-06-10 18:43:03 UTC  
> Merged at: 2025-06-10 18:43:00 UTC  
> Closed at: 2025-06-10 18:43:00 UTC  
> Url: https://github.com/boostorg/redis/pull/250  

async_exec now supports partial and total cancellation when the request is pending  
Added docs on async_exec's cancellation support  
Added detail::exec_fsm and macros for coroutine emulation  
Requests are now removed from the multiplexer when an error is encountered

---

## Review 1 [Commented]

> Username: mzimbres  
> Created_at: 2025-05-25 19:44:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/250#pullrequestreview-2866927298  

📁 include/boost/redis/detail/exec_fsm.hpp

```diff
  98 |+                return exec_action{
  99 |+                   elem_->get_error(),
 100 |+                   elem_->get_error() ? 0u : elem_->get_read_size()};
```

> Username: mzimbres  
> Created_at: 2025-05-25 19:34:21 UTC  
> Updated_at: 2025-05-25 19:44:49 UTC  
> Url: https://github.com/boostorg/redis/pull/250#discussion_r2106288111  

I think here you could return whatever is in `elem_->get_read_size()`. Seting to `0` when there is an error seems unnecessary and might indeed erase useful information.


---

## Comment 2

> Username: anarthal  
> Created_at: 2025-06-07 14:08:12 UTC  
> Url: https://github.com/boostorg/redis/pull/250#issuecomment-2952526511  

Today is national's PR bombardment day :)  
  
Quick walkthrough of this PR:  
  
* The main point is trying to split algorithms in `async_compose` into two parts, to make testing easier. The logic is extracted into a finite-state machine (`exec_fsm` in this case). The FSM is implemented as a coroutine. When the FSM needs to do something "non-local" (like invoking I/O, communicating with another task, etc), it yields, returning an action (`exec_action` in this case). The action contains all the information about what should be done. With this setup, `exec_op` becomes "dumb": it just invokes the FSM and executes the requested action.  
* In tests, we invoke the FSM directly, simulating different conditions, like cancellations. Tests are dead simple and reliable, but require a little bit of setup to simulate the right conditions (e.g. they create a multiplexer and invoke the functions that a reader and a writer would).  
* Ideally, I'd have used `asio::coroutine` for the FSM. However, this is implemented internally using `__COUNTER__` on Windows, which causes trouble (see https://github.com/boostorg/mysql/issues/259 for an in-depth explanation). So I've created `BOOST_REDIS_YIELD` that does almost the same, but without this problem (I already use this everywhere in MySQL).  
* `exec_action` and `exec_action_type` create together a sort of Rust-like enum, to store what to do next. This is lighter at compile-time and easier to use than `std::variant`.  
* I've added a `multiplexer::elem::is_done` new status to track when something is done. This simplifies the algorithm.  
* The behavior on cancellation should almost match what we already had, with some fixes. For instance, calling `reset_cancellation_state` is required if we want to support cancellations other than `terminal`.  
* I've used Boost.Core lightweight_test instead of Boost.Test because it's lighter at compile time. I can revert to Boost.Test if you prefer it.  
* I've added some extra tests for cancellation, just in case.

---

## Review 3 [Approved]

> Username: mzimbres  
> Created_at: 2025-06-08 12:22:54 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/redis/pull/250#pullrequestreview-2908420406  

📁 include/boost/redis/detail/exec_fsm.hpp

```diff
  32 |+    immediate,           // Invoke asio::async_immediate to avoid re-entrancy problems
  33 |+    done,                // Call the final handler
  34 |+    write,               // Notify the writer task
```

> Username: mzimbres  
> Created_at: 2025-06-08 11:51:58 UTC  
> Updated_at: 2025-06-08 12:22:54 UTC  
> Url: https://github.com/boostorg/redis/pull/250#discussion_r2134653835  

I think the naming here could be more descriptive `notify_writer` for example.


📁 include/boost/redis/impl/multiplexer.ipp

```diff
 120 |    if (ec) {
 121 |       reqs_.front()->notify_error(ec);
 122 |+       reqs_.pop_front();
```

> Username: mzimbres  
> Created_at: 2025-06-08 12:08:39 UTC  
> Updated_at: 2025-06-08 12:22:54 UTC  
> Url: https://github.com/boostorg/redis/pull/250#discussion_r2134660809  

Interesting, this is probably fixing a bug.

> Username: anarthal  
> Created_at: 2025-06-09 18:42:39 UTC  
> Url: https://github.com/boostorg/redis/pull/250#discussion_r2136265104  

It probably fixes potential UB in `multiplexer::cancel_on_conn_lost()` when the connection is lost due to a parsing error.


---

## Comment 4

> Username: mzimbres  
> Created_at: 2025-06-08 12:23:32 UTC  
> Url: https://github.com/boostorg/redis/pull/250#issuecomment-2954027783  

Very good job, thanks!

---

## Comment 5

> Username: mzimbres  
> Created_at: 2025-06-08 13:59:42 UTC  
> Url: https://github.com/boostorg/redis/pull/250#issuecomment-2954094667  

@anarthal The implementation in `exec_fsm.hpp` should also be moved to `exec_fsm.ipp` to improve compilation times.

---

## Review 6 [Commented]

> Username: mzimbres  
> Created_at: 2025-06-10 10:59:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/250#pullrequestreview-2913048148  

📁 include/boost/redis/impl/exec_fsm.ipp

```diff
  81 |+          if (!!(cancel_state & asio::cancellation_type_t::terminal)) {
  82 |+             BOOST_REDIS_YIELD(resume_point_, 5, exec_action_type::cancel_run)
  83 |+             elem_.reset();  // Deallocate memory before finalizing
```

> Username: mzimbres  
> Created_at: 2025-06-10 10:59:24 UTC  
> Url: https://github.com/boostorg/redis/pull/250#discussion_r2137582835  

AFAICS this is going to address https://github.com/boostorg/redis/issues/140 so after merge we can close that issue too.


---
