# #303 Adds custom setup requests [Merged]

> Username: anarthal  
> Created at: 2025-09-11 17:52:56 UTC  
> Updated at: 2025-09-15 12:19:25 UTC  
> Merged at: 2025-09-15 12:15:11 UTC  
> Closed at: 2025-09-15 12:15:12 UTC  
> Url: https://github.com/boostorg/redis/pull/303  

Adds config::setup and config::use_setup, to run arbitrary Redis commands on connection establishment  
Improves docs for config::{username, password, clientname, database_index}  
Splits all connection establishment tests into test_conn_hello  
  
close #302

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-09-11 18:16:12 UTC  
> Url: https://github.com/boostorg/redis/pull/303#issuecomment-3282137693  

Some notes  
  
* `detail::request_access` is currently used to workaround the restriction that only `HELLO` requests might have their priority set. It's used to expose a method outside request, but maintaining it private to the library. My intention is deprecating `request::config::hello_with_priority` and making it something internal. You proposed a `nice` value, but I think it's prone to misuse, and it'd impose extra overhead by having to order every request in the queue.  
* I haven't deprecated any config members yet because using `config::setup` is not straightforward without the command wrappers, and it'd require examples on how to do it now, at least.

---

## Review 2 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-13 16:04:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/303#pullrequestreview-3221060716  

📁 include/boost/redis/config.hpp

```diff
  23 |+    req.push("HELLO", "3");
  24 |+    return req;
  25 |+ }
```

> Username: mzimbres  
> Created_at: 2025-09-13 16:04:36 UTC  
> Url: https://github.com/boostorg/redis/pull/303#discussion_r2346809414  

By default I prefer to move every implementation to an `.ipp` file.


---

## Review 3 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-13 16:07:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/303#pullrequestreview-3221063004  

📁 include/boost/redis/config.hpp

```diff
 145 |+     * If set to true, the @ref setup member will be sent to the server immediately after
 146 |+     * connection establishment. Every time a reconnection happens, the setup
 147 |+     * request will be executed. This request can be used to perform authentication,
```

> Username: mzimbres  
> Created_at: 2025-09-13 16:07:00 UTC  
> Url: https://github.com/boostorg/redis/pull/303#discussion_r2346810261  

I would add  **before any other requests.** at the end of  
  
> Every time a reconnection happens, the setup request will be executed.


---

## Review 4 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-13 16:11:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/303#pullrequestreview-3221067115  

📁 include/boost/redis/connection.hpp

```diff
 340 |+       // clang-format off
 341 |+       // The hello pipeline might contain commands even when config::use_hello is false
 342 |+       // (e.g. AUTH and SELECT).
```

> Username: mzimbres  
> Created_at: 2025-09-13 16:11:29 UTC  
> Updated_at: 2025-09-13 16:11:30 UTC  
> Url: https://github.com/boostorg/redis/pull/303#discussion_r2346812332  

I am a bit confused by this comment. First I think it means `use_setup` and not `use_hello`. Second it seems to mean  
> when config::use_hello is **true**  
  
and not  
> when config::use_hello is **false**  
  
Also, it implies that in some cases we will be sending both the internal `hello_req_` and the `setup` requests, but when I look at the implementation I see  
```cpp  
if (cfg.use_setup) {  
   // Use the provided request, but setting the priority flag,  
   // even if the request contains no HELLO  
   // TODO: could we make this better?  
   req = std::move(cfg.setup);  
   request_access::set_hello_priority(req, true);  
}  
```  
That means, either one or the other is used but they are never merged.

> Username: anarthal  
> Created_at: 2025-09-14 11:58:17 UTC  
> Updated_at: 2025-09-14 11:58:19 UTC  
> Url: https://github.com/boostorg/redis/pull/303#discussion_r2347287720  

This was an unintended leftover from my previous implementation. It made no sense. Thanks for the catch.


---

## Review 5 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-13 16:13:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/303#pullrequestreview-3221069309  

📁 include/boost/redis/detail/hello_utils.hpp

```diff
  15 | 
  16 |- void setup_hello_request(config const& cfg, request& req);
  16 |+ void setup_hello_request(config& cfg, request& req);  // might move cfg.setup, for efficiency
```

> Username: mzimbres  
> Created_at: 2025-09-13 16:13:38 UTC  
> Updated_at: 2025-09-13 16:13:39 UTC  
> Url: https://github.com/boostorg/redis/pull/303#discussion_r2346813514  

I prefer not to move in cases where it does not matter from the performance point of view. Can we however just drop [hello_req_](https://github.com/boostorg/redis/blob/0cf2441ed2b81506f4b6f626d01ea43785ba31a9/include/boost/redis/connection.hpp#L89) entirely and use `config::setup`  when `use_setup = false`? We make a config copy internally so it does not matter if we change it.


---

## Comment 6

> Username: mzimbres  
> Created_at: 2025-09-13 16:19:29 UTC  
> Url: https://github.com/boostorg/redis/pull/303#issuecomment-3288591511  

Thanks. We could perhaps also remove [this](https://github.com/boostorg/redis/blob/0cf2441ed2b81506f4b6f626d01ea43785ba31a9/include/boost/redis/request.hpp#L326-L327) that sets priority conditionally only if is a HELLO command. Looks like an unnecessary complication.

---

## Comment 7

> Username: mzimbres  
> Created_at: 2025-09-13 16:24:38 UTC  
> Url: https://github.com/boostorg/redis/pull/303#issuecomment-3288595915  

> You proposed a nice value, but I think it's prone to misuse, and it'd impose extra overhead by having to order every request in the queue.  
  
We already have `O(n)` from the `std::stable_partition` so sorting would only add an extra `Olog(n))` to the complexity. But I agree there isn't any strong usecase for it so far.

---

## Comment 8

> Username: anarthal  
> Created_at: 2025-09-14 12:12:19 UTC  
> Url: https://github.com/boostorg/redis/pull/303#issuecomment-3289493012  

> Thanks. We could perhaps also remove [this](https://github.com/boostorg/redis/blob/0cf2441ed2b81506f4b6f626d01ea43785ba31a9/include/boost/redis/request.hpp#L326-L327) that sets priority conditionally only if is a HELLO command. Looks like an unnecessary complication.  
  
Removing the check breaks everything because the priority flag defaults to true in the constructor. Changing it to false is technically a breaking change. In the next PR I will deprecate the priority functions and remove all this in a subsequent release.

---

## Comment 9

> Username: anarthal  
> Created_at: 2025-09-14 12:21:57 UTC  
> Updated_at: 2025-09-14 12:22:10 UTC  
> Url: https://github.com/boostorg/redis/pull/303#issuecomment-3289498934  

> We already have O(n) from the std::stable_partition so sorting would only add an extra Olog(n)) to the complexity. But I agree there isn't any strong usecase for it so far.  
  
I don't know to which extent these O(n) and O(nlogn) are comparable. `stable_partition` is called in:  
  
* `cancel_on_conn_lost`: called once per disconnection. Never during regular operation. We can likely neglect this cost.  
* `cancel_waiting`: similar argument to the above.  
* `release_push_requests`: called once per write. There is no limit to the number of requests a write may have.  
  
On the other hand, sorting price gets paid once per added request, rather than once per write. So there's a factor that this analysis is not considering.  
  
I _think_ the multiplexer can be modified to use linked lists instead of a vector, and that would yield better efficiency. But I'd need to write the code and benchmark it to be sure, so I haven't done anything yet. In any case, I wouldn't pay this price, at least until some user asks for it.

---

## Comment 10

> Username: anarthal  
> Created_at: 2025-09-14 12:52:32 UTC  
> Url: https://github.com/boostorg/redis/pull/303#issuecomment-3289520982  

I think I've addressed all your comments. I've also done a general renaming removing "hello" and "handshake" in favor of "setup request".

---

## Review 11 [Approved]

> Username: mzimbres  
> Created_at: 2025-09-14 17:12:38 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/redis/pull/303#pullrequestreview-3221069745  

📁 include/boost/redis/impl/hello_utils.ipp

```diff
  12 | 
  11 |- void setup_hello_request(config const& cfg, request& req)
  13 |+ void setup_hello_request(config& cfg, request& req)
```

> Username: mzimbres  
> Created_at: 2025-09-13 16:14:21 UTC  
> Updated_at: 2025-09-14 17:12:38 UTC  
> Url: https://github.com/boostorg/redis/pull/303#discussion_r2346813735  

I think this should be renamed to `set_request_priority`.


---

## Comment 12

> Username: mzimbres  
> Created_at: 2025-09-14 17:13:29 UTC  
> Url: https://github.com/boostorg/redis/pull/303#issuecomment-3289700178  

Thanks.

---
