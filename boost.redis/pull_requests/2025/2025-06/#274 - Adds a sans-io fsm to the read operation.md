# #274 Adds a sans-io fsm to the read operation. [Merged]

> Username: mzimbres  
> Created at: 2025-06-16 21:41:02 UTC  
> Updated at: 2025-06-23 20:40:43 UTC  
> Merged at: 2025-06-23 20:40:42 UTC  
> Closed at: 2025-06-23 20:40:43 UTC  
> Url: https://github.com/boostorg/redis/pull/274  

@anarthal: Notes for the code review  
   - Adds a fsm for read operation following your work on `exec_fsm`.  
   - Log every event of the `reader_fsm` after it resumes, which improves log information content.  
   - The logger interface has to be extended with types that are actually in the `detail` namespace, so I make fsm actions a public type, this is needed to interpret log messages. I would like to do this to the `exec_fsm` too once a log object becomes available for `async_exec`, so I did not change your code yet.  
   - Cancellation hasn't been addresses yet for read operation. This will need more thought and a separate PR.  
  
Example log for the subscriber example. Once all ops are refactored to use an fsm, the log will become very detailed and more helpful  
```  
(Boost.Redis) resolve results: 127.0.0.1:6379  
(Boost.Redis) connected to 127.0.0.1:6379  
(Boost.Redis) fsm action: (reader_action::type::setup_cancellation, 0, Success)  
(Boost.Redis) fsm action: (reader_action::type::append_some, 0, Success)  
(Boost.Redis) writer_op: 117 bytes written.  
(Boost.Redis) fsm action: (reader_action::type::notify_push_receiver, 36, Success)  
(Boost.Redis) fsm action: (reader_action::type::append_some, 0, Success)  
(Boost.Redis) hello_op: Success  
subscribe channel 1  
^C  
(Boost.Redis) ping_op (4): Operation canceled  
(Boost.Redis) check_timeout_op (2): Operation canceled  
(Boost.Redis) fsm action: (reader_action::type::cancel_run, 0, Success)  
(Boost.Redis) fsm action: (reader_action::type::done, 0, Operation canceled)  
(Boost.Redis) writer_op (3): connection is closed.  
```

---

## Review 1 [Approved]

> Username: anarthal  
> Created_at: 2025-06-19 18:34:49 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/redis/pull/274#pullrequestreview-2943767807  

This looks good in general. In the longer run, I'd like to:  
  
* Avoid the `cancel_run` action completely in the reader and writer - I believe code would be cleaner if it was the parallel group that handled this.  
* Dismantle `async_append_some` and merge it into the reader (`async_compose` has some overhead).  
  
But this is not in scope for this PR.

📁 include/boost/redis/fsm_action.hpp

```diff
  37 |+    };
  38 |+ 
  39 |+    reader_action(
```

> Username: anarthal  
> Created_at: 2025-06-19 18:11:07 UTC  
> Updated_at: 2025-06-19 18:34:50 UTC  
> Url: https://github.com/boostorg/redis/pull/274#discussion_r2157480100  

If you're going to only have one constructor, better make all members public, provide no constructors and make the type an aggregate. It'd make sense to make a constructor if you think of the action as a variant-like type - e.g. having a constructor taking only an `error_code` that sets the type to `done`. Up to you. If you choose that path, I'd make the constructor inline - I don't think the gains in compile time here compensate the runtime cost of separate compilation.

> Username: mzimbres  
> Created_at: 2025-06-21 09:25:19 UTC  
> Url: https://github.com/boostorg/redis/pull/274#discussion_r2159988396  

Ok. With your log PR I can also move this to the detail namespace too since reader_action won't then appear in the public API.


📁 include/boost/redis/connection.hpp

```diff
 239 |+          switch (act.type_) {
 240 |+             case reader_action::type::setup_cancellation:
 241 |+                self.reset_cancellation_state(asio::enable_total_cancellation());
```

> Username: anarthal  
> Created_at: 2025-06-19 18:13:15 UTC  
> Updated_at: 2025-06-19 18:34:50 UTC  
> Url: https://github.com/boostorg/redis/pull/274#discussion_r2157482065  

Maybe partial cancellation makes more sense here? In any case, we'll handle this later.


📁 test/CMakeLists.txt

```diff
  39 | make_test(test_any_adapter)
  40 | make_test(test_exec_fsm)
  41 |+ make_test(test_reader_fsm)
```

> Username: anarthal  
> Created_at: 2025-06-19 18:16:58 UTC  
> Updated_at: 2025-06-19 18:34:50 UTC  
> Url: https://github.com/boostorg/redis/pull/274#discussion_r2157485389  

Since this test does not require a real server, please add it to test/Jamfile, too.


📁 include/boost/redis/impl/exec_fsm.ipp

```diff
  92 |+    case exec_action_type::elem: return "exec_action_type::" #elem
  93 |+ 
  94 |+ auto to_string(exec_action_type t) noexcept -> char const*
```

> Username: anarthal  
> Created_at: 2025-06-19 18:18:46 UTC  
> Updated_at: 2025-06-19 18:34:50 UTC  
> Url: https://github.com/boostorg/redis/pull/274#discussion_r2157486962  

Do we need this in the public headers at this point, or is it only used in the tests? IIRC the exec FSM doesn't have access to the logger at this point.


📁 test/test_reader_fsm.cpp

```diff
 146 |+    test_read_error();
 147 |+    test_parse_error();
 148 |+ 
```

> Username: anarthal  
> Created_at: 2025-06-19 18:30:41 UTC  
> Updated_at: 2025-06-19 18:34:50 UTC  
> Url: https://github.com/boostorg/redis/pull/274#discussion_r2157499251  

I'd add the following cases:  
  
* An incomplete message is delivered (i.e. it's fragmented in two reads).  
* The channel notification fails


📁 include/boost/redis/impl/logger.ipp

```diff
 103 |- 
 104 |-    std::clog << std::endl;
  99 |+    std::clog << "fsm action: (" << to_string(action.type_) << ", " << action.push_size_ << ", "
```

> Username: anarthal  
> Created_at: 2025-06-19 18:32:08 UTC  
> Updated_at: 2025-06-19 18:34:50 UTC  
> Url: https://github.com/boostorg/redis/pull/274#discussion_r2157500540  

I've got the concern that we might be logging too much for the `level::info` level. It looks to me that most of this info really belongs to `level::debug`. However, fine-tuning this is probably better done once we merge the logger interface PR, because we can start moving logging to fsms (and hence testing it).


---

## Comment 2

> Username: mzimbres  
> Created_at: 2025-06-21 21:49:49 UTC  
> Url: https://github.com/boostorg/redis/pull/274#issuecomment-2993786212  

I think I addressed all your points. Please have a look at it again. Are you about to get the log PR merged? I will wait and then rebase on top of your changes so that I can remove the usage of types in `detail` in the logger interface.

---

## Comment 3

> Username: anarthal  
> Created_at: 2025-06-22 11:40:40 UTC  
> Url: https://github.com/boostorg/redis/pull/274#issuecomment-2994141825  

Looks good. I've still some work to do, as CIs don't pass yet and there's a ton of tests to write. I think it's better if you merge/squash merge it and I'll move all action types back to detail as part of the logger PR.

---
