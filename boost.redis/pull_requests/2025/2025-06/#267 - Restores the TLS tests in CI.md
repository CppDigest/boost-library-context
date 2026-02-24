# #267 Restores the TLS tests in CI [Merged]

> Username: anarthal  
> Created at: 2025-06-07 13:13:18 UTC  
> Updated at: 2025-06-07 16:37:16 UTC  
> Merged at: 2025-06-07 16:37:09 UTC  
> Closed at: 2025-06-07 16:37:09 UTC  
> Url: https://github.com/boostorg/redis/pull/267  

Renews test certificates  
Recovers and refactors test_conn_tls.cpp  
Adds a test for TLS reconnection

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-06-07 13:30:57 UTC  
> Url: https://github.com/boostorg/redis/pull/267#issuecomment-2952493435  

This PR entails no functional change. It makes TLS in the CI Redis servers functional, restores the TLS tests that we had, and adds some extra checks. This is going to be handy to test the changes I need to do to implement UNIX sockets properly (they involve refactoring this part).

---
