# #380 Reworks test_conn_push and test_conn_push2 [Merged]

> Username: anarthal  
> Created at: 2026-01-14 13:51:48 UTC  
> Updated at: 2026-01-14 20:54:39 UTC  
> Merged at: 2026-01-14 20:54:34 UTC  
> Closed at: 2026-01-14 20:54:34 UTC  
> Url: https://github.com/boostorg/redis/pull/380  

Adds a test to verify that async_receive (v1) is cancelled on reconnection  
Adds a test to verify that a reconnection is triggered when the receive adapter generates an error  
Makes the unsubscribe and push adapter tests live only in test_conn_push2, since they test functionality common to async_receive and async_receive2  
Migrates all tests to lightweight_test  
  
Entails no functional change

---

## Comment 1

> Username: anarthal  
> Created_at: 2026-01-14 13:52:44 UTC  
> Url: https://github.com/boostorg/redis/pull/380#issuecomment-3749663576  

I'd like to merge this before tacking #331, to verify I don't break anything.

---
