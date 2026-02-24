# #408 - Expose connection_id access [Closed]

> Username: r33s3n6  
> Created at: 2025-02-03 04:59:09 UTC  
> Updated at: 2025-02-05 14:14:07 UTC  
> Closed at: 2025-02-05 14:14:07 UTC  
> Url: https://github.com/boostorg/mysql/issues/408  

**Description:**    
Users may need to terminate long-running queries by executing `KILL <connection_id>` in another connection, which requires access to the `connection_id`. While this ID can be obtained using `SELECT CONNECTION_ID();`, the server actually provides the `connection_id` during the handshake phase—we simply ignore it.    
  
Would it be possible to store the `connection_id` in `connection_state_data` and expose a getter in `any_connection` for easier access?    
  
  
Looking forward to your feedback on this proposal. Thanks!

---

## Comment 1

> Username: anarthal  
> Created at: 2025-02-03 09:00:36 UTC  
> Url: https://github.com/boostorg/mysql/issues/408#issuecomment-2630337493  

This definitely makes sense.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-02-03 16:34:10 UTC  
> Url: https://github.com/boostorg/mysql/issues/408#issuecomment-2631500829  

I've been investigating this and found something surprising: according to [the official MySQL C API docs](https://dev.mysql.com/doc/c-api/8.0/en/mysql-thread-id.html), `mysql_thread_id` (which presumably uses the 4-byte connection ID value provided by the handshake) can yield incorrect results if thread IDs exceed UINT32_MAX. They suggest to use `SELECT CONNECTION_ID()` instead, but that's an extra round-trip to the server.  
  
I'm reluctant to expose this because this looks like something that might backfire people easily. I'm running a test to see what happens when connection IDs overflow, but it's taking some time (after all, establishing UINT32_MAX connections takes time). On the other hand, it looks like other languages (like PHP) ignore this and use the 32-bit value (see [`mysqli::$thread_id`](https://www.php.net/manual/en/mysqli.thread-id.php). What are your thoughts about this?

---

## Comment 3

> Username: r33s3n6  
> Created at: 2025-02-03 17:06:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/408#issuecomment-2631579194  

[Relnote of MySQL 5.7.5](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-5.html)  
  
> **Connection IDs are 32-bit unsigned integers, beginning at 1. When the server assigns connection IDs and reaches the top of the 32-bit range, it rolls the value over to begin at 1 again**. It was possible that the server would assign a connection ID to a new thread while that ID was still in use by an existing thread, if the old thread was particularly long running. For such cases, reference to the ID becomes ambiguous. For example, it cannot reliably be determined for KILL connection_id which thread to kill, which could lead to undefined behavior. **This behavior has been corrected so that in-use IDs are not reused. (WL #7293)**  
  
[WL #7293](https://dev.mysql.com/worklog/task/?id=7293) also discusses this issue, suggesting that thread IDs are constrained within the range of uint32 for compatibility reasons. Additionally, the description of my_thread_id() appears to be somewhat outdated (written before 5.7.5?). Another relevant [discussion](https://bugs.mysql.com/bug.php?id=44167) can be found here.

---

## Comment 4

> Username: anarthal  
> Created at: 2025-02-03 17:59:05 UTC  
> Url: https://github.com/boostorg/mysql/issues/408#issuecomment-2631693857  

Oh! Thanks for your research.  
  
I'm happy to implement it then. Hopefully this week.

---

## Comment 5

> Username: r33s3n6  
> Created at: 2025-02-04 03:22:47 UTC  
> Url: https://github.com/boostorg/mysql/issues/408#issuecomment-2632731602  

Looking forward to it :)

---

## Comment 6

> Username: anarthal  
> Created at: 2025-02-04 20:33:46 UTC  
> Url: https://github.com/boostorg/mysql/issues/408#issuecomment-2635007042  

If you can have a look at the PR, it'd be great.

---

## Comment 7

> Username: r33s3n6  
> Created at: 2025-02-05 02:16:21 UTC  
> Url: https://github.com/boostorg/mysql/issues/408#issuecomment-2635550143  

Thanks! I’ve reviewed the PR, and it looks great—well done!
