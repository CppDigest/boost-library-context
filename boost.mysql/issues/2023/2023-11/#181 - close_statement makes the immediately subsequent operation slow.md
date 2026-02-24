# #181 - close_statement makes the immediately subsequent operation slow [Closed]

> Username: anarthal  
> Created at: 2023-11-13 15:25:17 UTC  
> Updated at: 2024-01-14 13:14:27 UTC  
> Closed at: 2024-01-14 13:14:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/181  

* When running a close statement op, the immediately subsequent operation takes a big amount of time (~40ms).  
* This is due to the fact that close statement doesn't send any response back. We wait until the server sends a TCP-level ACK, which happens ~40ms later.  
* The official C API doesn't suffer this. From my tests, it looks like they don't wait for the entire packet to be written before writing the next one. From Asio's API, doing this reliable doesn't look viable.  
* A possible solution is to send a pipelined COM_PING packet with the close statement request. Ping is very lightweight and forces the server to write an OK packet.  
* Tested with the async API `async_close_statement`, but very much likely affects the sync API, too.  
* Tested under Ubuntu 22.04 (Linux 6.2.0).

---

## Comment 1

> Username: sheldonlyr  
> Created at: 2023-11-30 01:20:37 UTC  
> Url: https://github.com/boostorg/mysql/issues/181#issuecomment-1832954259  

How can we avoid this performance issue on application level? Any suggestions?

---

## Comment 2

> Username: anarthal  
> Created at: 2023-11-30 10:43:11 UTC  
> Url: https://github.com/boostorg/mysql/issues/181#issuecomment-1833509737  

At this moment, you can use `reset_connection`/`async_reset_connection`, which doesn't suffer from this. This function will wipe any session state modifications from the connection, including prepared statements. This has the neat effect of batch-closing all statements you opened.  
  
On the other hand, [async_]`reset_connection` wipes all session variables, too. This includes any `time_zone` or character settings you did. The character set/collation will get reset to the server's default, and not to whatever you specified in `handshake_params` (MySQL being MySQL). You can retrieve which charset will your server use by running `SELECT @@global.character_set_client, @@global.character_set_connection, @@global.character_set_results;`.  
  
If you're using single-use connections (you're calling `close`, then connecting them again), you don't have to call [async_]`close_statement` at all.  
  
If you can provide more information about your use case, I may be able to give you some extra ideas.

---

## Comment 3

> Username: sheldonlyr  
> Created at: 2023-12-01 01:08:48 UTC  
> Url: https://github.com/boostorg/mysql/issues/181#issuecomment-1835238815  

Hi! Thanks for so quick answer！

---

## Comment 4

> Username: sheldonlyr  
> Created at: 2023-12-01 01:12:50 UTC  
> Url: https://github.com/boostorg/mysql/issues/181#issuecomment-1835242340  

Oh, one more question😅 Does connection pool can be used in Boost 1.84? I'm really looking forward to the addition of this feature.

---

## Comment 5

> Username: anarthal  
> Created at: 2023-12-01 10:04:17 UTC  
> Url: https://github.com/boostorg/mysql/issues/181#issuecomment-1835811353  

I'm afraid it will be 1.85. I'm currently finishing docs and tests for the feature, so it can't make the times required by Boost for 1.84. I expect it to be in master in a few weeks though, and I'd be more than happy to have some early feedback.
