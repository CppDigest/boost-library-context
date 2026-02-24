# #228 Uses asio::deferred_t as default completion type. [Merged]

> Username: mzimbres  
> Created at: 2024-12-23 21:11:55 UTC  
> Updated at: 2025-02-08 21:26:12 UTC  
> Merged at: 2025-02-08 21:26:11 UTC  
> Closed at: 2025-02-08 21:26:12 UTC  
> Url: https://github.com/boostorg/redis/pull/228  



---

## Comment 1

> Username: mzimbres  
> Created_at: 2024-12-28 11:22:10 UTC  
> Url: https://github.com/boostorg/redis/pull/228#issuecomment-2564300586  

Hi @anarthal, does this fix the issue? Thanks.

---

## Comment 2

> Username: anarthal  
> Created_at: 2024-12-28 11:30:49 UTC  
> Updated_at: 2024-12-28 11:30:59 UTC  
> Url: https://github.com/boostorg/redis/pull/228#issuecomment-2564302053  

From what I read from my cell phone I'd say it does. I'm planning on reviewing it from my PC on Monday.

---

## Comment 3

> Username: anarthal  
> Created_at: 2024-12-30 09:22:19 UTC  
> Url: https://github.com/boostorg/redis/pull/228#issuecomment-2565228068  

I've left a couple of suggestions. They're not at all needed for this to work, you can do them if/whenever you want.  
  
Also, I'd also write a couple of tests to cover that all the functions you changed successfully support the default token. That might be even a piece of code that you don't run, just to check it builds.  
  
As I said, they're suggestions, the changes look good as they are.

---

## Review 4 [Commented]

> Username: anarthal  
> Created_at: 2024-12-30 09:22:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/228#pullrequestreview-2525359499  

📁 include/boost/redis/connection.hpp

```diff
1254 |-    auto async_run(config const& cfg, logger l, CompletionToken token)
1254 |+    template <class CompletionToken = asio::deferred_t>
1255 |+    auto async_run(config const& cfg, logger l, CompletionToken token = {})
```

> Username: anarthal  
> Created_at: 2024-12-30 09:19:07 UTC  
> Updated_at: 2024-12-30 09:22:31 UTC  
> Url: https://github.com/boostorg/redis/pull/228#discussion_r1899404261  

As minor comments, consider unifying all signatures using completion tokens to `CompletionToken&&`

---

📁 include/boost/redis/connection.hpp

```diff
1284 |-    template <class Response, class CompletionToken>
1285 |-    auto async_exec(request const& req, Response& resp, CompletionToken&& token)
1277 |+    template <class Response, class CompletionToken = asio::deferred_t>
```

> Username: anarthal  
> Created_at: 2024-12-30 09:19:41 UTC  
> Updated_at: 2024-12-30 09:22:31 UTC  
> Url: https://github.com/boostorg/redis/pull/228#discussion_r1899404623  

You can also consider using `BOOST_ASIO_COMPLETION_TOKEN_FOR` to implement concept checks.


---
