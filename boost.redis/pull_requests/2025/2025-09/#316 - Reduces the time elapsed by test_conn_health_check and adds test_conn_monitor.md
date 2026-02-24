# #316 Reduces the time elapsed by test_conn_health_check and adds test_conn_monitor [Merged]

> Username: anarthal  
> Created at: 2025-09-23 19:12:22 UTC  
> Updated at: 2025-09-27 16:13:05 UTC  
> Merged at: 2025-09-27 16:13:02 UTC  
> Closed at: 2025-09-27 16:13:02 UTC  
> Url: https://github.com/boostorg/redis/pull/316  

Splits test_conn_health_check into a test to verify that the health checker works and another to verify that MONITOR is handled correctly.  
Tests no longer wait or use CLIENT PAUSE

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-09-23 19:14:06 UTC  
> Url: https://github.com/boostorg/redis/pull/316#issuecomment-3325234133  

This is an intent to make test_conn_health_check more workable. It now uses a simple BLPOP that will wait forever and cause the connection to be killed. Runtime is down from 30s to 1s.  
  
Since this test was covering MONITOR, I've added a test that just does this. This test runs almost instantly.

---

## Review 2 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-27 15:58:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/316#pullrequestreview-3275001122  

📁 test/test_conn_check_health.cpp

```diff
  26 | namespace {
  27 | 
  34 |- struct push_callback {
```

> Username: mzimbres  
> Created_at: 2025-09-27 15:58:56 UTC  
> Url: https://github.com/boostorg/redis/pull/316#discussion_r2384226283  

Glad this is being removed, it is a mess.


---

## Review 3 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-27 16:03:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/316#pullrequestreview-3275002377  

📁 test/test_conn_monitor.cpp

```diff
  31 |+ // Verifies that using the MONITOR command works properly.
  32 |+ // Opens a connection, issues a MONITOR, issues some commands to
  33 |+ // generate some traffic, and waits for several MONITOR messages to arrive.
```

> Username: mzimbres  
> Created_at: 2025-09-27 16:03:42 UTC  
> Url: https://github.com/boostorg/redis/pull/316#discussion_r2384227828  

> // Opens a connection, issues a MONITOR, issues some commands to  
> // generate some traffic, and waits for several MONITOR messages to arrive.  
  
I thought it was not allowed to issue commands on the same connection one MONITOR mode is entered. Perhaps this was only a restriction in RESP2 since it does not support server pushes.


---
