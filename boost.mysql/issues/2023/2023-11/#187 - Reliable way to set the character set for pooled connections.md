# #187 - Reliable way to set the character set for pooled connections [Closed]

> Username: anarthal  
> Created at: 2023-11-29 15:47:23 UTC  
> Updated at: 2024-03-05 11:54:19 UTC  
> Closed at: 2024-03-05 11:54:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/187  

`connection_pool` employs `async_reset_connection`, which discards character set settings used during connection establishment (sigh). This makes the connection use the server's default character set (as given by `SELECT @@global.character_set_client, @@global.character_set_connection, @@global.character_set_results;`), which is, in most cases, `latin1` (sigh).  
  
`pool_params` should probably have a `std::string charset` parameter to run a `SET NAMES` statement on connection establishment and reset.  
  
Descoped from #19.
