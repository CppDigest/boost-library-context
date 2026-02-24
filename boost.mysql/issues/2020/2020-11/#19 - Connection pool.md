# #19 - Connection pool [Closed]

> Username: anarthal  
> Created at: 2020-11-29 17:34:58 UTC  
> Updated at: 2023-12-26 17:16:39 UTC  
> Closed at: 2023-12-26 17:16:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/19  

Provide a mechanism to create a connection pool to re-use existing connections.

---

## Comment 1

> Username: anarthal  
> Created at: 2022-05-18 21:58:20 UTC  
> Url: https://github.com/boostorg/mysql/issues/19#issuecomment-1130588951  

From the review: prepared statements are allocated at the `connection` level. When taking a connection from the pool, preparing some statements, using them and then returning a connection to the pool without closing it, those statements may be leaked if `prepared_statement::close` is not called. When implementing this, we should create some mechanism that stores which statements have been prepared by which connection, so they can be closed when the connection is returned to the pool. We may want to close those statements by server-side ID (as returned by `prepared_statement::id()`, vs. by using the `prepared_statement` object), and in batch.

---

## Comment 2

> Username: anarthal  
> Created at: 2023-06-11 15:19:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/19#issuecomment-1586212133  

Connections that are iddle for too long are automatically closed by MySQL (see #161). The pool should provide an option for auto-keepalives after a certain period has elapsed.

---

## Comment 3

> Username: banderzhm  
> Created at: 2023-08-25 07:27:58 UTC  
> Url: https://github.com/boostorg/mysql/issues/19#issuecomment-1692898527  

Is there any plan to support connection pooling in the future? Will this feature be implemented only after 'std:: execution' is supported?

---

## Comment 4

> Username: anarthal  
> Created at: 2023-08-29 10:46:11 UTC  
> Url: https://github.com/boostorg/mysql/issues/19#issuecomment-1697199289  

Sorry for the late reply. Yes, there is. I hope to get it in 1.84.

---

## Comment 5

> Username: banderzhm  
> Created at: 2023-09-13 03:00:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/19#issuecomment-1716869800  

That's great！

---

## Comment 6

> Username: anarthal  
> Created at: 2023-12-26 17:16:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/19#issuecomment-1869673660  

Closed by bc5577ede56204770fb35c296cdeb21c3c772a36 (typo in commit message prevented auto close).
