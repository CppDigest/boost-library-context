# #539 Replace the dependency on asio with asio_core [Open]

> Username: Lastique  
> Created at: 2025-12-24 14:06:54 UTC  
> Updated at: 2026-02-11 02:50:29 UTC  
> Url: https://github.com/boostorg/process/pull/539  

Boost.Process doesn't use any advanced Boost.ASIO features and switching to asio_core removes the transitive dependencies on Boost.Context and Boost.DateTime.

---
