# #291 Removes handshaker in favor of asio::deferred [Merged]

> Username: anarthal  
> Created at: 2025-07-27 20:52:24 UTC  
> Updated at: 2025-09-04 14:48:07 UTC  
> Merged at: 2025-09-04 14:48:00 UTC  
> Closed at: 2025-09-04 14:48:00 UTC  
> Url: https://github.com/boostorg/redis/pull/291  

* Refactors the handshake process to use asio::deferred instead of a custom composed operation.  
* Fixes logging on HELLO error (close #297)  
* Fixes a potential problem on reconnection after a HELLO error is encountered (close #290)  
* Fixes a race condition in the health checker that could cause it to never exit on cancellation  
* Adds support for users with a username different than "default" and an empty password (close #298)  
* Adds integration testing for authentication  
* Adds unit testing for the hello utility functions

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-07-27 20:53:28 UTC  
> Url: https://github.com/boostorg/redis/pull/291#issuecomment-3124721237  

This is still early work, but the idea is getting rid of the entire handshaker class, since the same functionality can be implemented in terms of `asio::deferred`. I think I've found a bug (see #290) while looking at the code.

---

## Comment 2

> Username: mzimbres  
> Created_at: 2025-07-28 09:34:17 UTC  
> Url: https://github.com/boostorg/redis/pull/291#issuecomment-3126376719  

Much welcome, thanks.

---

## Comment 3

> Username: anarthal  
> Created_at: 2025-09-04 11:41:49 UTC  
> Url: https://github.com/boostorg/redis/pull/291#issuecomment-3253283091  

I've fixed all the small bits I've found around HELLO when doing the refactor.

---

## Comment 4

> Username: anarthal  
> Created_at: 2025-09-04 14:47:23 UTC  
> Url: https://github.com/boostorg/redis/pull/291#issuecomment-3254087478  

Merging per Marcelo's request.

---
