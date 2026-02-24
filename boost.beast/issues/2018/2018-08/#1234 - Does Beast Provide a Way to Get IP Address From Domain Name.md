# #1234 - Does Beast Provide a Way to Get IP Address From Domain Name? [Closed]

> Username: DragonOsman  
> Created at: 2018-08-26 21:01:16 UTC  
> Updated at: 2018-09-26 18:40:44 UTC  
> Closed at: 2018-09-26 18:40:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1234  

My question is as the title suggests: Is there a way in Beast to get an IP address from a domain name, or do I have to find out how to do this manually?  Thanks in advance for replies.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-08-26 22:27:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1234#issuecomment-416077359  

Beast doesn't know anything about TCP/IP or domain names. You should already be familiar with Boost.Asio, the class you are looking for is `boost::asio::ip::tcp::resolver`. Also, the Beast examples demonstrate how to perform DNS lookups:  
https://github.com/boostorg/beast/blob/1da229a27c6f0539d422bcedbcf47cfe2517164a/example/http/client/sync/http_client_sync.cpp#L57
