# #392 Adds request::hello and deprecates config::username, password [Merged]

> Username: anarthal  
> Created at: 2026-01-29 19:23:28 UTC  
> Updated at: 2026-01-30 18:31:03 UTC  
> Merged at: 2026-01-30 18:30:59 UTC  
> Closed at: 2026-01-30 18:30:59 UTC  
> Url: https://github.com/boostorg/redis/pull/392  

Adds request::hello and request::hello_setname  
Deprecates config::{username, password, client_name, database_index}  
Adds a documentation page on authentication

---

## Review 1 [Commented]

> Username: mzimbres  
> Created_at: 2026-01-29 22:29:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/392#pullrequestreview-3725352686  

📁 doc/modules/ROOT/pages/auth.adoc

```diff
  31 |+ cfg.setup.hello("my_username", "my_password");
  32 |+ 
  33 |+ conn.async_run(cfg, asio::detached);
```

> Username: mzimbres  
> Created_at: 2026-01-29 22:29:13 UTC  
> Url: https://github.com/boostorg/redis/pull/392#discussion_r2743809440  

This might be a dangerous suggestion for newcomers. As alternative I see  
1. Add a comment: `// make sure conn outlives the async_run operation.`  
2. `conn->async_run(cfg, consign(detached, conn));`  
3. `co_await conn->async_run(cfg);`

> Username: anarthal  
> Created_at: 2026-01-30 10:23:26 UTC  
> Updated_at: 2026-01-30 10:23:27 UTC  
> Url: https://github.com/boostorg/redis/pull/392#discussion_r2745592406  

Using `co_await` since I find it to be the less distracting.


---
