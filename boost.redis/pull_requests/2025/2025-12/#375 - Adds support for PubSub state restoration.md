# #375 Adds support for PubSub state restoration [Merged]

> Username: anarthal  
> Created at: 2025-12-19 13:22:28 UTC  
> Updated at: 2026-01-09 20:08:58 UTC  
> Merged at: 2026-01-09 20:08:54 UTC  
> Closed at: 2026-01-09 20:08:54 UTC  
> Url: https://github.com/boostorg/redis/pull/375  

Adds config::restore_pubsub_state and command_context  
Deprecates the boost_redis_to_bulk extension point taking a std::string& argument, in favor of an extension point with the same name taking a command_context argument  
  
close #367

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-12-19 13:32:08 UTC  
> Url: https://github.com/boostorg/redis/pull/375#issuecomment-3675104403  

@mzimbres this is a prototype of the pubsub state restoration feature. I'd like to get this done before the next release, so we can make `async_receive2` not cancel at all during reconnections.  
  
The idea is that, when adding a `SUBSCRIBE` (or related) command to a `request`, we keep track of the channels this request is subscribing to (or unsubscribing from). When the request succeeds, we store the channels in a `std::set`. Every time a reconnection happens, we compose commands to subscribe to the channels the user was using before the reconnection.  
  
This requires inspecting command arguments from `request`. It's something the current `boost_redis_to_bulk` extension point makes difficult, since the request is never passed to the function. I've deprecated it in favor of a new extension point, with the same name, but taking a `command_context` argument. This is a class wrapping `request` that allows extension points to add arguments to a command. We need to inspect command arguments if we ever intend to support cluster.  
  
This is still missing docs and tests - let me know if you think this is the right approach before spending time on these. Also, I can split the PR into smaller ones (e.g. deprecating the old extension point first) if you think it's best.

---

## Review 2 [Commented]

> Username: mzimbres  
> Created_at: 2025-12-20 11:06:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/375#pullrequestreview-3601055029  

📁 include/boost/redis/config.hpp

```diff
 348 |+ 
 349 |+    // (TODO: document properly) Do we want to re-subscribe to channels on reconnection?
 350 |+    bool restore_pubsub_state = false;
```

> Username: mzimbres  
> Created_at: 2025-12-20 11:06:15 UTC  
> Url: https://github.com/boostorg/redis/pull/375#discussion_r2637048363  

I think there should be no option to deactivate restoration in the connection. We can offer a `request` option e.g. `request::config::track_subscriptions`.


---

## Review 3 [Commented]

> Username: mzimbres  
> Created_at: 2025-12-20 11:09:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/375#pullrequestreview-3601055902  

📁 include/boost/redis/detail/pubsub_state.hpp

```diff
  22 |+ enum class pubsub_change_type;
  23 |+ 
  24 |+ class pubsub_state {
```

> Username: mzimbres  
> Created_at: 2025-12-20 11:09:14 UTC  
> Url: https://github.com/boostorg/redis/pull/375#discussion_r2637049539  

Perhpas name it `subscriptions` or `subscription _tracker`?


---

## Review 4 [Commented]

> Username: mzimbres  
> Created_at: 2025-12-20 11:11:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/375#pullrequestreview-3601056467  

📁 include/boost/redis/impl/pubsub_state.ipp

```diff
  36 |+ void pubsub_state::commit_changes(const request& req)
  37 |+ {
  38 |+    for (const auto& ch : detail::request_access::pubsub_changes(req))
```

> Username: mzimbres  
> Created_at: 2025-12-20 11:11:11 UTC  
> Url: https://github.com/boostorg/redis/pull/375#discussion_r2637050426  

```diff  
-   for (const auto& ch : detail::request_access::pubsub_changes(req))  
+   for (const auto& ch : request_access::pubsub_changes(req))  
```


---

## Comment 5

> Username: mzimbres  
> Created_at: 2025-12-20 11:34:03 UTC  
> Url: https://github.com/boostorg/redis/pull/375#issuecomment-3677750421  

This will be a great improvement over the current complex resubscribe loop, so you have my approval. I don't understand yet why `boost_redis_to_bulk` is a problem. We can intercept `SUBSCRIBE` arguments on `request::push/range`. How idoes `boost_redis_to_bulk` poses a problem there?

---

## Comment 6

> Username: anarthal  
> Created_at: 2025-12-20 14:05:14 UTC  
> Url: https://github.com/boostorg/redis/pull/375#issuecomment-3677846994  

> This will be a great improvement over the current complex resubscribe loop, so you have my approval. I don't understand yet why `boost_redis_to_bulk` is a problem. We can intercept `SUBSCRIBE` arguments on `request::push/range`. How idoes `boost_redis_to_bulk` poses a problem there?  
  
At the point of `push`, the argument is a custom user type, not a string. After `boost_redis_to_bulk`, we have a serialized blob string (that is, length, separator, actual string, separator). To get the actual string value, we would have to parse the serialized blob string. That's not very elegant (or efficient).

---

## Comment 7

> Username: mzimbres  
> Created_at: 2025-12-20 14:27:47 UTC  
> Url: https://github.com/boostorg/redis/pull/375#issuecomment-3677860811  

> At the point of push, the argument is a custom user type, not a string. After boost_redis_to_bulk, we have a serialized blob string (that is, length, separator, actual string, separator). To get the actual string value, we would have to parse the serialized blob string. That's not very elegant (or efficient).  
  
I think we should avoid all this trouble by adding  `request::subcribe/unsubcribe/...` member functions where we can intercept the channels more easily. Parsing the payload to find out the channels sounds defeating, those functions are too generic to work with, it has been the plan since the start to add more specialized request members, which are friendlier to the user.

---

## Comment 8

> Username: anarthal  
> Created_at: 2025-12-20 14:34:24 UTC  
> Url: https://github.com/boostorg/redis/pull/375#issuecomment-3677864821  

This does work for this task, but idk if it does for cluster (I don't have the answer to this question, maybe it does). Cluster needs to know the key each command is acting on and compute its hash to know where to direct the command to.  
  
We could support some syntax like  
  
```  
request.push_command(subscribe{"hello"});  
```  
  
Although you probably need a regular and a range version of the command.

---

## Comment 9

> Username: mzimbres  
> Created_at: 2025-12-20 21:39:44 UTC  
> Url: https://github.com/boostorg/redis/pull/375#issuecomment-3678140330  

> We could support some syntax like  
> request.push_command(subscribe{"hello"});  
> Although you probably need a regular and a range version of the command.  
  
This looks interesting because with reflection we could perhaps generate these structs automatically from a description doc (I would call it `push2`). I am wondering whether the two steps to call push2 wouldn't hurt usability: first construct subscribe than push it.

---

## Comment 10

> Username: anarthal  
> Created_at: 2025-12-20 22:13:53 UTC  
> Url: https://github.com/boostorg/redis/pull/375#issuecomment-3678162069  

ATM reflection can only generate aggregates. I lack the vision to know if that'd be enough (it wouldn't if we need a templated range, for instance).  
  
It'd much better for usability having functions in request, actually. But then request is gonna end up having zounds of functions, and not all of them used frequently.

---

## Comment 11

> Username: anarthal  
> Created_at: 2025-12-20 22:24:15 UTC  
> Url: https://github.com/boostorg/redis/pull/375#issuecomment-3678167940  

How about  
  
```cpp  
req.push2(subscribe, {"channel", "channel2"});  
```  
  
Where subscribe is an object of type  
  
```cpp  
struct subscribe_t {  
    void operator ()(request& r, initializer_list<string_view>);  
    // maybe other overloads  
};  
```  
  
so push2 can just call into the passed object forwarding all the args. This way we can define each command (or family of commands) in a separate header.

---

## Comment 12

> Username: mzimbres  
> Created_at: 2025-12-20 22:42:22 UTC  
> Url: https://github.com/boostorg/redis/pull/375#issuecomment-3678177876  

> It'd much better for usability having functions in request, actually. But then request is gonna end up having zounds of functions, and not all of them used frequently.  
  
This is what I like most so far. I don't mind having lots of member function and overloads for each command. The complexity is not our invention but intrinsic to Redis. The only question is what to do when Valkey and Redis have a different definition for the same command.

---

## Comment 13

> Username: anarthal  
> Created_at: 2025-12-21 10:15:34 UTC  
> Url: https://github.com/boostorg/redis/pull/375#issuecomment-3678663349  

How about  
  
```cpp  
req.push2(subscribe).with({"ch1", "ch2"});  
```  
  
The difference is that we avoid forwarding args in push2. This allows us to use things like initializer lists without incurring in problems.  
  
I'm wary of adding too many member functions to request. We can easily end up with 1000 member functions if we end up adding support for all commands, each one having different overloads. This may not be the best for compile times.

---

## Comment 14

> Username: anarthal  
> Created_at: 2026-01-08 19:37:21 UTC  
> Url: https://github.com/boostorg/redis/pull/375#issuecomment-3725390440  

@mzimbres I've implemented this in terms of `request::subscribe` and friends. This has now tests and docs and is ready to be reviewed. I plan to change `async_receive2` to no longer complete on reconnection, but it'll be in a separate PR (this is big enough already).

---
