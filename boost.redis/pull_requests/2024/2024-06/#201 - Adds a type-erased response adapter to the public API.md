# #201 Adds a type-erased response adapter to the public API [Merged]

> Username: anarthal  
> Created at: 2024-06-09 11:26:33 UTC  
> Updated at: 2025-09-22 10:23:41 UTC  
> Merged at: 2024-12-22 10:43:39 UTC  
> Closed at: 2024-12-22 10:43:39 UTC  
> Url: https://github.com/boostorg/redis/pull/201  

Would something along these lines suit you @mzimbres? If it does, I'll clean it up and write docs & tests for it.  
  
close #128

---

## Comment 1

> Username: mzimbres  
> Created_at: 2024-06-09 15:06:03 UTC  
> Url: https://github.com/boostorg/redis/pull/201#issuecomment-2156643052  

@anarthal Thanks, see my comments.

---

## Comment 2

> Username: anarthal  
> Created_at: 2024-06-09 16:23:36 UTC  
> Url: https://github.com/boostorg/redis/pull/201#issuecomment-2156687037  

Did you publish your comments? I can't see any of them

---

## Review 3 [Commented]

> Username: mzimbres  
> Created_at: 2024-06-09 18:24:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/201#pullrequestreview-2106256942  

📁 include/boost/redis/detail/connection_base.hpp

```diff
 461 |+    {
 462 |+       return async_exec(req, any_adapter(resp), std::forward<CompletionToken>(token));
 463 |+    }
```

> Username: mzimbres  
> Created_at: 2024-06-09 14:50:27 UTC  
> Updated_at: 2024-06-09 18:24:43 UTC  
> Url: https://github.com/boostorg/redis/pull/201#discussion_r1632316856  

This overload is not needed, the `connection_base` class is not meant to be used directly.


📁 include/boost/redis/connection.hpp

```diff
 411 |+ 
 412 |+    template <class CompletionToken>
 413 |+    auto async_exec(request const& req, any_adapter adapter, CompletionToken token)
```

> Username: mzimbres  
> Created_at: 2024-06-09 15:01:59 UTC  
> Updated_at: 2024-06-09 18:24:43 UTC  
> Url: https://github.com/boostorg/redis/pull/201#discussion_r1632318958  

This function should now be implemented in terms of any_completion_handler so that the instantiation can be moved to the .ipp file, similar to how async_run is implemented (see above). That is IMO the main motivation for these changes: reduce compilation times.


---

## Comment 4

> Username: mzimbres  
> Created_at: 2024-08-04 18:22:15 UTC  
> Url: https://github.com/boostorg/redis/pull/201#issuecomment-2267627850  

Are you still going to work on this PR?

---

## Comment 5

> Username: anarthal  
> Created_at: 2024-08-04 19:46:29 UTC  
> Url: https://github.com/boostorg/redis/pull/201#issuecomment-2267648899  

whoa, I completely forgot about this! Yes, I'll finish it this incoming week.

---

## Comment 6

> Username: anarthal  
> Created_at: 2024-08-07 17:46:17 UTC  
> Url: https://github.com/boostorg/redis/pull/201#issuecomment-2274003020  

@mzimbres this is now complete on my side. Please let me know if any changes are required.

---

## Comment 7

> Username: mzimbres  
> Created_at: 2024-08-07 20:09:21 UTC  
> Url: https://github.com/boostorg/redis/pull/201#issuecomment-2274262911  

Many thanks! Give me some days to review it. I would also say you this is worth listing in the Changelog.

---

## Review 8 [Commented]

> Username: mzimbres  
> Created_at: 2024-08-10 19:45:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/201#pullrequestreview-2231681327  

📁 include/boost/redis/connection.hpp

```diff
 416 |+    /// Calls `boost::redis::basic_connection::async_exec`.
 417 |+    template <class CompletionToken>
 418 |+    auto async_exec(request const& req, any_adapter adapter, CompletionToken&& token)
```

> Username: mzimbres  
> Created_at: 2024-08-10 19:45:16 UTC  
> Updated_at: 2024-08-10 19:58:42 UTC  
> Url: https://github.com/boostorg/redis/pull/201#discussion_r1712696018  

I did not document the adapters and I don't see any immediate need if this overload for users, The main idea of this PR is to improve compilation times. I think we should wait until someone asks for an adapter interface, that will only happen when people want to pass their own data structures in.  
  
Edit: Make adapter overloads private for now.

> Username: anarthal  
> Created_at: 2024-08-11 14:56:00 UTC  
> Url: https://github.com/boostorg/redis/pull/201#discussion_r1713003200  

Wasn't this PR actually motivated by a user asking for this exact functionality? See comments in https://github.com/boostorg/redis/issues/128


---

## Review 9 [Commented]

> Username: mzimbres  
> Created_at: 2024-08-10 19:56:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/201#pullrequestreview-2231682174  

📁 include/boost/redis/adapter/any_adapter.hpp

```diff
  59 |+ 
  60 |+     template <class Executor>
  61 |+     friend class detail::connection_base;
```

> Username: mzimbres  
> Created_at: 2024-08-10 19:56:32 UTC  
> Updated_at: 2024-08-10 19:56:33 UTC  
> Url: https://github.com/boostorg/redis/pull/201#discussion_r1712700840  

I think there should be no `friend` class here, the class should define   
  
```cpp  
void operator(std::size_t, resp3::basic_node<std::string_view> const&, system::error_code&)  
```  
  
and forward the call directly to `impl_`. Once you do that you can also remove [this](https://github.com/boostorg/redis/blob/e8dd4d69eb3524c9a0efb0c9723790b479a4ba8f/include/boost/redis/detail/connection_base.hpp#L515) typedef and replace it with `any_adapter` AFAICS.  
  
In a later issue we will have to think about how this relates to the [receiver adapter](https://github.com/boostorg/redis/blob/e8dd4d69eb3524c9a0efb0c9723790b479a4ba8f/include/boost/redis/detail/connection_base.hpp#L516) or whether we need another any adapter class. This will be needed to type erase the receive operation too

> Username: anarthal  
> Created_at: 2024-08-11 14:56:53 UTC  
> Updated_at: 2024-08-11 14:57:05 UTC  
> Url: https://github.com/boostorg/redis/pull/201#discussion_r1713003447  

This makes sense only if `any_adapter` is a public class - otherwise I can remove it (together with the Doxygen docs). See my question above.

> Username: mzimbres  
> Created_at: 2024-10-20 19:23:13 UTC  
> Updated_at: 2024-10-20 19:23:14 UTC  
> Url: https://github.com/boostorg/redis/pull/201#discussion_r1807926443  

Ok, let us keep it public.


---

## Comment 10

> Username: anarthal  
> Created_at: 2024-12-02 11:42:37 UTC  
> Url: https://github.com/boostorg/redis/pull/201#issuecomment-2511308309  

This is ready

---
