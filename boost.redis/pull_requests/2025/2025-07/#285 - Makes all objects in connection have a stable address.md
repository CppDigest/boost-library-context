# #285 Makes all objects in connection have a stable address [Merged]

> Username: anarthal  
> Created at: 2025-07-14 16:50:44 UTC  
> Updated at: 2025-07-25 20:51:26 UTC  
> Merged at: 2025-07-25 20:51:14 UTC  
> Closed at: 2025-07-25 20:51:14 UTC  
> Url: https://github.com/boostorg/redis/pull/285  

Moves read_buffer memory reservation to the connection's constructor  
Makes read_buffer memory reservation size be a power of 2

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-07-14 16:52:55 UTC  
> Url: https://github.com/boostorg/redis/pull/285#issuecomment-3070277264  

This is intended to alleviate one of the problems you had in #283, and make reasoning about lifetimes trivial.  
  
Before I add some tests to verify that moving `connection`s is fine, do you think this is the way to go?  
  
I could also make `exec_op`, `reader_op`, `writer_op` and friends operate on `connection_impl` rather than `connection`, which should be marginally more efficient, but requires more work. Let me know if you think it's worth it.

---

## Comment 2

> Username: mzimbres  
> Created_at: 2025-07-16 08:09:01 UTC  
> Url: https://github.com/boostorg/redis/pull/285#issuecomment-3077473142  

AFAICS this is only needed if we want to support a connection being moved while `async_run` is pending. Or is there anything else? Is it reasonable to allow that?

---

## Comment 3

> Username: anarthal  
> Created_at: 2025-07-16 09:17:34 UTC  
> Url: https://github.com/boostorg/redis/pull/285#issuecomment-3077704046  

It's aimed to solve things like this: https://github.com/boostorg/redis/pull/283#discussion_r2198105774  
  
This would cause a segfault after moving the connection, whether you perform the move before, during or after async_run executes. IMO not having to reason about lifetimes at all makes things much easier.

---

## Comment 4

> Username: mzimbres  
> Created_at: 2025-07-16 10:06:44 UTC  
> Url: https://github.com/boostorg/redis/pull/285#issuecomment-3077863984  

Hi Ruben, I am going to address those comments in the way you suggested. Just don't have much time now. I agree what you propose here is good (and common) practice and does not have any bad side effects. I support further development here, thanks.

---

## Comment 5

> Username: anarthal  
> Created_at: 2025-07-16 11:30:33 UTC  
> Url: https://github.com/boostorg/redis/pull/285#issuecomment-3078161156  

Great. I will do some further fixes here and post it for review again.  
  
I'm going to be out of office during the weekend too, so don't worry.

---

## Comment 6

> Username: anarthal  
> Created_at: 2025-07-25 11:01:36 UTC  
> Url: https://github.com/boostorg/redis/pull/285#issuecomment-3117361425  

This is now ready @mzimbres. I've made all composed operations use `connection_impl`, and moved some of the functionality required by them there. I've also moved the reserve() call to the connection's constructor.

---

## Review 7 [Commented]

> Username: mzimbres  
> Created_at: 2025-07-25 12:02:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/285#pullrequestreview-3055142182  

📁 include/boost/redis/connection.hpp

```diff
 393 |-       writer_timer_.expires_at((std::chrono::steady_clock::time_point::max)());
 394 |-    }
 506 |+    : impl_(new detail::connection_impl<Executor>{std::move(ex), std::move(ctx), std::move(lgr)})
```

> Username: mzimbres  
> Created_at: 2025-07-25 12:02:40 UTC  
> Updated_at: 2025-07-25 12:02:41 UTC  
> Url: https://github.com/boostorg/redis/pull/285#discussion_r2230912262  

`make_unique`?

> Username: anarthal  
> Created_at: 2025-07-25 14:23:16 UTC  
> Url: https://github.com/boostorg/redis/pull/285#discussion_r2231227101  

What would be the benefit? When using a unique_ptr as a variable, it saves you from spelling the type twice:  
  
```cpp  
std::unique_ptr<int> myint ( new int{42} );  
auto myint = std::make_unique<int>(42);  
```  
  
But here it seems to add nothing:  
  
```  
impl_(new detail::connection_impl<Executor>{std::move(ex), std::move(ctx), std::move(lgr)})  
impl_(std::make_unique<detail::connection_impl<Executor>>{std::move(ex), std::move(ctx), std::move(lgr)})  
```  
  
Note that `make_unique` doesn't improve efficiency as `make_shared` does.

> Username: mzimbres  
> Created_at: 2025-07-25 19:24:16 UTC  
> Url: https://github.com/boostorg/redis/pull/285#discussion_r2231859227  

Correct, in this case I would only use it to avoid direct usages of the `new` operator.


---

## Review 8 [Commented]

> Username: mzimbres  
> Created_at: 2025-07-25 19:33:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/285#pullrequestreview-3056601512  

📁 include/boost/redis/connection.hpp

```diff
  93 |+    executor_type get_executor() noexcept { return writer_timer_.get_executor(); }
  94 |+ 
  95 |+    struct exec_op {
```

> Username: mzimbres  
> Created_at: 2025-07-25 19:33:07 UTC  
> Url: https://github.com/boostorg/redis/pull/285#discussion_r2231873972  

Why is `exec_op` embedded in the `connection_impl` while other op implementations are outside? It uses  
`connection_impl* obj_ ;` instead of `connection_impl<Executor>* obj_;`

> Username: anarthal  
> Created_at: 2025-07-25 19:54:49 UTC  
> Updated_at: 2025-07-25 19:54:50 UTC  
> Url: https://github.com/boostorg/redis/pull/285#discussion_r2231905237  

Because it's used in connection_impl::async_exec, which is in turn needed to do the HELLO and the pings. The other ops are not.  
  
connection_impl without template arguments in the context of the class is a shorthand for connection_impl<Executor>(see https://stackoverflow.com/questions/12810630/how-do-i-refer-to-a-class-template-as-a-template-from-within-its-own-class-def)

> Username: anarthal  
> Created_at: 2025-07-25 19:55:42 UTC  
> Url: https://github.com/boostorg/redis/pull/285#discussion_r2231906680  

My long-term plan is to place all ops within connection_impl once they're small enough and using FSMs, but I didn't want to change too much.


---
