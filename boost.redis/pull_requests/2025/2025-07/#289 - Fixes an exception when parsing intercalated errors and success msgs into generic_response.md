# #289 Fixes an exception when parsing intercalated errors and success msgs into generic_response [Merged]

> Username: anarthal  
> Created at: 2025-07-27 17:30:17 UTC  
> Updated at: 2025-09-01 18:24:09 UTC  
> Merged at: 2025-09-01 18:18:35 UTC  
> Closed at: 2025-09-01 18:18:35 UTC  
> Url: https://github.com/boostorg/redis/pull/289  

close #287

---

## Review 1 [Commented]

> Username: mzimbres  
> Created_at: 2025-07-28 09:42:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/289#pullrequestreview-3061596691  

📁 test/test_conn_exec_error.cpp

```diff
 317 |+       exec_finished = true;
 318 |+       conn.cancel();
 319 |+    });
```

> Username: mzimbres  
> Created_at: 2025-07-28 09:42:37 UTC  
> Url: https://github.com/boostorg/redis/pull/289#discussion_r2235608442  

Early feedback: when `async_exec` fails to execute the commands of a request it should complete with an error. I think all adapters work like this.

> Username: anarthal  
> Created_at: 2025-07-28 10:20:42 UTC  
> Updated_at: 2025-07-28 10:20:43 UTC  
> Url: https://github.com/boostorg/redis/pull/289#discussion_r2235741499  

How the hell do tests pass then? lol

> Username: anarthal  
> Created_at: 2025-07-28 10:25:26 UTC  
> Updated_at: 2025-07-28 10:25:27 UTC  
> Url: https://github.com/boostorg/redis/pull/289#discussion_r2235757509  

Hm, none of general_aggregate nor general_simple seem to be setting the output error code on failure.


---

## Comment 2

> Username: anarthal  
> Created_at: 2025-09-01 10:10:36 UTC  
> Url: https://github.com/boostorg/redis/pull/289#issuecomment-3241746149  

What should we do with this @mzimbres? It looks like adapters don't set the output error code when they receive an individual error. So `async_exec` succeeds, but the obtained response is an error. This kind of makes sense, since there was no networking problem, but the server answered with an error.

---

## Comment 3

> Username: mzimbres  
> Created_at: 2025-09-01 17:38:55 UTC  
> Url: https://github.com/boostorg/redis/pull/289#issuecomment-3242997583  

Sorry, I forgot about this. I think in my early design `async_exec` would always complete with an error if the command execution failed. If it did not, users would have to check both the completion error `ec` AND the response error. An exception to this rule occurs for the `generic_response` that can receive responses from multiple commands. In this case if some commands fail we don't want to complete with an error because it might be misleading, see https://github.com/boostorg/redis/issues/129. But I believe there are inconsistencies in my implementation, for example, I am not sure `response<T1, T2, T3, ...>` will behave exactly like `generic_response` in regards to error but they should.  
  
We might have to complement the docs stating that for single command requests a command error will be mapped in a completion error and that for multi-command requests users have to check `ec` and the individual response errors.  
  
In any case this PR seems to go in the correct direction, thanks.

---
