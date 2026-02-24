# #345 Adds Sentinel support [Merged]

> Username: anarthal  
> Created at: 2025-10-30 13:56:08 UTC  
> Updated at: 2025-11-19 21:31:23 UTC  
> Merged at: 2025-11-19 21:31:19 UTC  
> Closed at: 2025-11-19 21:31:19 UTC  
> Url: https://github.com/boostorg/redis/pull/345  

close #237   
close #269   
close #268  
close #229

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-10-30 13:56:32 UTC  
> Url: https://github.com/boostorg/redis/pull/345#issuecomment-3468152093  

Still an early prototype, I want to check that my CI approach is viable.

---

## Review 2 [Commented]

> Username: mzimbres  
> Created_at: 2025-11-15 21:20:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/345#pullrequestreview-3468368687  

📁 include/boost/redis/config.hpp

```diff
  37 |+ }
  38 |+ 
  39 |+ /** @brief Compares two addresses for difference.
```

> Username: mzimbres  
> Created_at: 2025-11-15 21:20:02 UTC  
> Url: https://github.com/boostorg/redis/pull/345#discussion_r2530149758  

```diff  
-/** @brief Compares two addresses for difference.  
+/** @brief Compares two addresses for inequality.  
```


---

## Review 3 [Commented]

> Username: mzimbres  
> Created_at: 2025-11-15 21:40:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/345#pullrequestreview-3468390650  

> Username: mzimbres  
> Created_at: 2025-11-15 21:40:07 UTC  
> Updated_at: 2025-11-15 21:40:08 UTC  
> Url: https://github.com/boostorg/redis/pull/345#discussion_r2530172405  

Small thing that I think is more readable. Instead of  
  
```cpp  
enum class sentinel_action_type {...};  
  
class sentinel_action {  
   sentinel_action_type type_;  
   sentinel_action(sentinel_action_type type) noexcept  
}  
```  
  
I would write  
  
```cpp  
  
class sentinel_action {  
   enum class type {...};  
   type type_;  
   sentinel_action(type type) noexcept  
}  
```


---

## Review 4 [Commented]

> Username: mzimbres  
> Created_at: 2025-11-15 21:43:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/345#pullrequestreview-3468398855  

📁 include/boost/redis/detail/sentinel_resolve_fsm.hpp

```diff
  59 |+    sentinel_action_type type() const { return type_; }
  60 |+ 
  61 |+    system::error_code error() const
```

> Username: mzimbres  
> Created_at: 2025-11-15 21:43:17 UTC  
> Updated_at: 2025-11-15 21:43:18 UTC  
> Url: https://github.com/boostorg/redis/pull/345#discussion_r2530180647  

```diff  
-   system::error_code error() const  
+   [[nodiscard]]  
+   system::error_code error() const  
```


---

## Review 5 [Commented]

> Username: mzimbres  
> Created_at: 2025-11-15 21:52:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/345#pullrequestreview-3468428161  

📁 include/boost/redis/detail/exec_one_fsm.hpp

```diff
  48 |+ class exec_one_fsm {
  49 |+    int resume_point_{0};
  50 |+    any_adapter resp_;
```

> Username: mzimbres  
> Created_at: 2025-11-15 21:52:28 UTC  
> Updated_at: 2025-11-15 21:52:29 UTC  
> Url: https://github.com/boostorg/redis/pull/345#discussion_r2530209849  

```diff  
-   any_adapter resp_;  
+   any_adapter adapter_;  
```


---

## Review 6 [Commented]

> Username: mzimbres  
> Created_at: 2025-11-15 22:08:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/345#pullrequestreview-3468470536  

📁 include/boost/redis/impl/run_fsm.ipp

```diff
  68 |+       return {st.cfg.addr, st.cfg.use_ssl};
  69 |+    }
  70 |+ }
```

> Username: mzimbres  
> Created_at: 2025-11-15 22:08:35 UTC  
> Updated_at: 2025-11-15 22:10:31 UTC  
> Url: https://github.com/boostorg/redis/pull/345#discussion_r2530255579  

For readability I would remove the `!` in `if (!st.cfg.unix_socket.empty())` and swap the return statements.  
  
EDIT: Or perhaps add a `config` member `use_unix_sockets()`.


---

## Review 7 [Commented]

> Username: mzimbres  
> Created_at: 2025-11-15 22:29:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/345#pullrequestreview-3468520051  

📁 include/boost/redis/impl/sentinel_resolve_fsm.ipp

```diff
 125 |+          // Store the resulting address in a well-known place
 126 |+          if (st.cfg.sentinel.server_role == role::master) {
 127 |+             st.cfg.addr = resp.master_addr;
```

> Username: mzimbres  
> Created_at: 2025-11-15 22:29:30 UTC  
> Updated_at: 2025-11-15 22:32:39 UTC  
> Url: https://github.com/boostorg/redis/pull/345#discussion_r2530307347  

IMO this assignment should be moved to the `run_op` right after `async_sentinel_resolve` resumes to make that function more readable. It would be more intuitive if `async_sentinel_resolve` complete with the value instead of changing the `connection_impl` state. Can we change the completion signature to `void(error_code, address)` to achieve that?  
  
EDIT: We could also extend the completion signature with a third parameter which are the new sentinel addresses which is also use in run_op. This way the `async_sentinel_resolve` would not change any state in connection_impl.

> Username: anarthal  
> Created_at: 2025-11-19 16:19:26 UTC  
> Updated_at: 2025-11-19 16:19:28 UTC  
> Url: https://github.com/boostorg/redis/pull/345#discussion_r2542715040  

Moving the assignment or the update_sentinel_list to run makes run (which is already big) have even more responsibilities. The tests are already gigantic, so I'm not very keen on this.  
  
I don't think adding a 2nd (or 3rd) completion parameter to the run FSM makes a lot of good, because the parameter needs to be there for code paths, but has a valid value only in one of it. Unless you have a very clear opinion on this, I'd prefer for this to stay as is.


---

## Review 8 [Commented]

> Username: mzimbres  
> Created_at: 2025-11-15 22:44:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/345#pullrequestreview-3468558960  

📁 include/boost/redis/impl/sentinel_utils.hpp

```diff
  57 |+    BOOST_ASSERT(it->depth == 0u);
  58 |+    if (it->data_type != resp3::type::array)
  59 |+       return {error::invalid_data_type};
```

> Username: mzimbres  
> Created_at: 2025-11-15 22:44:22 UTC  
> Updated_at: 2025-11-15 22:44:23 UTC  
> Url: https://github.com/boostorg/redis/pull/345#discussion_r2530350331  

This is not the correct error code. The data type is valid, just not expected. From the current error codes `expects_resp3_aggregate`, is the one that describes better the problem, but feel free to add `expects_resp3_array` as we already have `expects_resp3_map`.

> Username: mzimbres  
> Created_at: 2025-11-16 20:14:55 UTC  
> Url: https://github.com/boostorg/redis/pull/345#discussion_r2532206385  

More background: The `invalid_data_type` was meant to be used when the wire value of the data type was not valid, i.e. not one of `$`, `+`, `-`, `%` etc. Perhaps we should also have a `unexpected_data_type` and drop the individual `unexpected_resp3_array`, `unexpected_resp3_map` etc. that is not scalable since it requires too many error codes.

> Username: anarthal  
> Created_at: 2025-11-19 15:39:09 UTC  
> Updated_at: 2025-11-19 15:39:10 UTC  
> Url: https://github.com/boostorg/redis/pull/345#discussion_r2542559965  

I've added `error::expects_resp3_array` and `error::expects_resp3_string`. If these ever come to pass, they yield better information than the single error code.


---

## Review 9 [Commented]

> Username: mzimbres  
> Created_at: 2025-11-15 22:44:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/345#pullrequestreview-3468560722  

📁 include/boost/redis/impl/sentinel_utils.hpp

```diff
  68 |+       BOOST_ASSERT(it->depth == 1u);
  69 |+       if (it->data_type != resp3::type::map)
  70 |+          return {error::invalid_data_type};
```

> Username: mzimbres  
> Created_at: 2025-11-15 22:44:59 UTC  
> Url: https://github.com/boostorg/redis/pull/345#discussion_r2530352394  

```diff  
-         return {error::invalid_data_type};  
+         return {error::expects_resp3_map};  
```


---

## Review 10 [Commented]

> Username: mzimbres  
> Created_at: 2025-11-15 22:50:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/345#pullrequestreview-3468575794  

📁 include/boost/redis/impl/sentinel_utils.hpp

```diff
  45 |+ 
  46 |+ // Parses a list of replicas or sentinels
  47 |+ inline system::error_code parse_server_list(
```

> Username: mzimbres  
> Created_at: 2025-11-15 22:50:48 UTC  
> Url: https://github.com/boostorg/redis/pull/345#discussion_r2530369547  

- Can we move this to .ipp?  
- Can we take a `std::vector<node>` instead and work with `.at()` function instead of iterators. We don't need performance here so I would prefer to be on the safer side.

> Username: anarthal  
> Created_at: 2025-11-19 17:00:59 UTC  
> Updated_at: 2025-11-19 17:01:00 UTC  
> Url: https://github.com/boostorg/redis/pull/345#discussion_r2542867146  

> Can we move this to .ipp?  
  
I don't think it makes sense because this is an `impl/` file. It gets included by `.ipp` files only, and is never seen by the end-user code.

> Username: anarthal  
> Created_at: 2025-11-19 17:14:22 UTC  
> Url: https://github.com/boostorg/redis/pull/345#discussion_r2542909645  

> Can we take a std::vector<node> instead and work with .at() function instead of iterators. We don't need performance here so I would prefer to be on the safer side.  
  
I'm gonna defer this to #355. `flat_tree` contains the number of messages, and this makes parsing much easier. None of these asserts can be triggered unless there's a bug in the parser, so it's not much of a problem. I will address it though, since you're right about the security/performance tradeoff here.


---

## Comment 11

> Username: mzimbres  
> Created_at: 2025-11-15 22:57:01 UTC  
> Url: https://github.com/boostorg/redis/pull/345#issuecomment-3537028473  

I am half way through it. The sentinel_utils.hpp implements hard stuff so I will have a more detailed look at it, perhaps tomorrow.

---

## Comment 12

> Username: mzimbres  
> Created_at: 2025-11-16 20:20:54 UTC  
> Url: https://github.com/boostorg/redis/pull/345#issuecomment-3539319549  

I finished reviewing. I haven't found anything serious to fix only things to consider. We can also do it in another PR to avoid delaying a merge, we have plenty of time until the next release. Many thanks you implement this pretty fast considering its size.

---

## Comment 13

> Username: mzimbres  
> Created_at: 2025-11-16 20:26:26 UTC  
> Url: https://github.com/boostorg/redis/pull/345#issuecomment-3539323745  

One last question, IIUC we resolve with the sentinel using the same `connection_impl` that we use to interact with the server. Is it safe for users to star async_exec, receive and cancellation on these ops while the master/replica is being resolved? Shouldn't we add a new connection_impl that is only used to resolve with sentinel? Or is it clear to you that mixing these ops won't interfere with each other?

---

## Comment 14

> Username: mzimbres  
> Created_at: 2025-11-19 10:10:41 UTC  
> Url: https://github.com/boostorg/redis/pull/345#issuecomment-3551882979  

When merged we can also close these tickets https://github.com/boostorg/redis/issues?q=is%3Aissue%20state%3Aopen%20label%3Asentinel

---

## Review 15 [Commented]

> Username: anarthal  
> Created_at: 2025-11-19 16:58:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/345#pullrequestreview-3483830303  

> One last question, IIUC we resolve with the sentinel using the same connection_impl that we use to interact with the server. Is it safe for users to star async_exec, receive and cancellation on these ops while the master/replica is being resolved? Shouldn't we add a new connection_impl that is only used to resolve with sentinel? Or is it clear to you that mixing these ops won't interfere with each other?  
  
It's safe. Actually, the exec test hits this situation, and I've added a receive test this for receive. exec only touches the multiplexer queue, and receive2 the channel. Everything else is managed by run. run ensures that you're either resolving addresses or communicating with the server, but not both.  
  
We could extract the read buffer from the multiplexer to make this point even clearer, since it's used by `async_exec_one`, and the current usage is a little bit weird. But I'll probably defer this to another PR.

---
