# #73 - Null Bulk String processing error [Closed]

> Username: ivs-77  
> Created at: 2023-02-26 15:18:03 UTC  
> Updated at: 2023-02-26 16:10:01 UTC  
> Closed at: 2023-02-26 16:10:00 UTC  
> Url: https://github.com/boostorg/redis/issues/73  

To reproduce just try to get non-existing key from redis:  
  
```  
      request req;  
      req.push("GET", "non-existing-key");  
      response<std::optional<std::string>> resp;  
      co_await conn->async_exec(req, resp);  
```  
  
Response from Redis for request above is "$-1\r\n" which means a Null Bulk String.  
But library throws exception "Can't convert string to number."  
It is because function boost::redis::resp3::to_int called for "-1" to find length of bulk string.

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-02-26 15:52:24 UTC  
> Url: https://github.com/boostorg/redis/issues/73#issuecomment-1445394480  

You are receiving a resp2 reply. Please upgrade the protocol to resp3 by sending the HELLO command with version 3.

---

## Comment 2

> Username: ivs-77  
> Created at: 2023-02-26 16:10:00 UTC  
> Url: https://github.com/boostorg/redis/issues/73#issuecomment-1445398792  

Thank you for quick response! It works now!
